# FRANXX PTERO — FX PROJECT

Private-use lightweight server and bot control panel.

## Included

- Login authentication with JWT
- Public registration disabled
- Root/Admin-only Profile page
- Root/Admin-only API / Access Keys page
- Root/Admin can create normal users or root/admin users
- Access keys with `users:create` and `servers:create` scopes
- Access-key secrets are shown once and only SHA-256 hashes are stored
- Account, server metadata, and access-key database stored directly on a Railway Persistent Volume (JSON files)
- No Supabase or GitHub database integration required
- Node.js / Python / Custom runtime commands
- Start / stop / restart server runtime
- Live WebSocket console + stdin
- Live RAM, CPU, storage and uptime monitor
- Configurable RAM allocation per server
- Multi-file upload
- ZIP upload + manual UNZIP
- Multi-select files
- Bulk delete
- Make/download selected files as ZIP
- File editor
- Server settings
- Responsive mobile UI
- FRANXX PTERO logo, banner and favicon branding
- Footer: `© 2025 - 2026 KYZX FRANXX DEV`

## Account JSON format

The user database uses the following shape. The `password` field contains a bcrypt hash, never the plaintext password:

```json
{
  "users": [
    {
      "id": "usr_xxx",
      "username": "admin",
      "password": "$2b$12$...",
      "root": true,
      "createdAt": "2026-10-02T00:00:00.000Z"
    }
  ]
}
```

The server also accepts the older top-level array format for backward compatibility. `password` must be a bcrypt hash, not plaintext.

Normal users use `root: false`.

Access keys are stored separately in `database/access-keys.json` using `{ "keys": [] }`; only the SHA-256 hash of the secret is stored.

## Railway storage configuration

Create a Railway Persistent Volume and mount it at `/data`. Set `DATA_DIR=/data` and `SERVERS_DIR=/data/servers` in Railway Variables. User accounts, server metadata, API keys, and uploaded server files are stored on this volume, so they survive redeploys. Set `ROOT_USERNAME`, `ROOT_PASSWORD`, and a long random `JWT_SECRET`. No Supabase project, Supabase keys, or GitHub database credentials are needed.

## Access-key API

Use:

```http
Authorization: Bearer fx_live_xxxxxxxxx
```

Create a normal user:

```http
POST /api/access/users
Content-Type: application/json

{
  "username": "botuser",
  "password": "strong-password"
}
```

Create a server for an existing user:

```http
POST /api/servers
Authorization: Bearer fx_live_xxxxxxxxx
Content-Type: application/json

{
  "userId": "usr_xxx",
  "name": "My Bot",
  "runtime": "node",
  "entry": "index.js",
  "command": "node index.js",
  "memoryLimit": 512
}
```

An access key cannot create a root account. Root elevation is only available from the root/admin panel.

## Run

```bash
npm install
npm start
```

Set `JWT_SECRET` in production and keep secrets in Railway Variables. Never commit `.env` or real tokens. Optional upload limit can be changed with `MAX_UPLOAD_MB`.

## Branding

Logo:
`https://zfile.web.id/PBe7sdw.png`

Banner:
`https://zfile.web.id/ptVnzVN.png`

## V2 UI / account isolation

- Create Server is accessed from the ☰ sidebar.
- Dashboard no longer shows a Create Server button.
- Header branding stays left-aligned on Android/mobile.
- Every server has an ownerId and all server/file/runtime APIs verify ownership.
- Upload storage also verifies server ownership before writing files.
