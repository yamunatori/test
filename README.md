# ⚡ NeonBoard — Red-DiscordBot Dashboard

A cyberpunk-themed desktop GUI for managing your Red-DiscordBot, built with **Electron + Anime.js v4** on the frontend and a **Red cog (REST + WebSocket)** on the backend.

---

## 📦 Package Structure

```
neonboard/
├── cog/
│   └── redbot_dashboard/       ← Drop this into your Red cogs folder
│       ├── __init__.py
│       ├── dashboard.py
│       └── info.json
└── electron-app/               ← The desktop app
    ├── package.json
    ├── main.js
    ├── preload.js
    └── src/
        └── index.html
```

---

## 🤖 Step 1 — Install the Red Cog

### Option A: Drop-in (manual)
1. Copy the `cog/redbot_dashboard/` folder into your Red cogs directory.
   - Default location: `~/.local/share/Red-DiscordBot/data/<instance>/cogs/`
   - Or wherever your custom cogs live.
2. In Discord, run:
   ```
   [p]load redbot_dashboard
   ```

### Option B: Via Downloader (if you host this on GitHub)
```
[p]repo add neonboard https://github.com/YOUR_REPO/neonboard
[p]cog install neonboard redbot_dashboard
[p]load redbot_dashboard
```

### Install psutil (for CPU/RAM metrics)
```bash
pip install psutil
```
or in the Red venv:
```bash
redbot-venv/bin/pip install psutil
```

---

## 🔑 Step 2 — Get Your API Token

In Discord, run:
```
[p]dashboard token
```
The bot will DM you a token. Copy it — you'll need it in the app.

---

## 💻 Step 3 — Run the Electron App

### Prerequisites
- [Node.js](https://nodejs.org/) v18+

### Install & run
```bash
cd electron-app
npm install
npm start
```

### Build a .exe (Windows)
```bash
npm run build:win
```
The installer will be in `electron-app/dist/`.

---

## 🔌 Connecting

1. Launch NeonBoard
2. In the **API URL** field enter: `http://127.0.0.1:8765`
   - Change this if you changed the port with `[p]dashboard port <port>`
3. Paste your token into the **API Token** field
4. Click **⚡ CONNECT**

---

## 🗂️ Dashboard Sections

| Section | What it shows |
|---|---|
| **Dashboard** | Bot overview — guilds, users, latency, cogs, uptime, live charts |
| **System Metrics** | CPU, RAM, disk, thread count (requires psutil) |
| **Commands** | All commands — search, filter by cog, enable/disable, view details |
| **Cog Manager** | All loaded cogs — reload or unload any cog live |
| **Guilds** | All servers — icon, member count, channels, roles, premium tier, full detail modal |
| **Bot Admins** | View and manage bot owner IDs |
| **Settings** | Change API URL/token, view bot system info |

---

## ⚡ Bot Commands

| Command | Description |
|---|---|
| `[p]dashboard token` | DM yourself the current API token |
| `[p]dashboard newtoken` | Rotate to a new token (invalidates old one) |
| `[p]dashboard port <port>` | Change the API port (reload cog to apply) |
| `[p]dashboard status` | Show server address, WS client count, psutil status |

---

## 🌐 API Reference

All endpoints require `Authorization: Bearer <token>`.

### REST
| Method | Path | Description |
|---|---|---|
| GET | `/api/status` | Bot overview stats |
| GET | `/api/metrics` | System metrics (psutil) |
| GET | `/api/guilds` | List all guilds |
| GET | `/api/guilds/{id}` | Guild detail |
| GET | `/api/guilds/{id}/members` | First 100 members |
| GET | `/api/commands` | All commands |
| POST | `/api/commands/{name}/toggle` | Toggle enable/disable |
| GET | `/api/cogs` | Loaded cogs |
| POST | `/api/cogs/{name}/load` | Load a cog |
| POST | `/api/cogs/{name}/unload` | Unload a cog |
| POST | `/api/cogs/{name}/reload` | Reload a cog |
| GET | `/api/admins` | Bot admins |
| POST | `/api/admins/add` | Add admin `{user_id}` |
| POST | `/api/admins/remove` | Remove admin `{user_id}` |
| GET | `/api/settings` | Server settings |
| POST | `/api/settings` | Update settings |

### WebSocket
Connect to `ws://127.0.0.1:8765/ws`.  
Send as first message: `{"token": "YOUR_TOKEN"}`  
Server broadcasts every 2s:
```json
{
  "type": "metrics",
  "latency_ms": 42.1,
  "guild_count": 5,
  "user_count": 1234,
  "cpu_percent": 12.3,
  "memory_mb": 256.4,
  "commands_used": 87,
  "timestamp": "2026-03-26T..."
}
```

---

## 🎨 Tech Stack

| Layer | Tech |
|---|---|
| Desktop shell | Electron 30 |
| Animations | **Anime.js v4** (named ESM imports) |
| Bot cog | Python, aiohttp, Red-DiscordBot 3.5.x |
| System metrics | psutil |
| Auth | Bearer token (stored in Red Config) |

---

## 🔒 Security Notes

- The API server only binds to `127.0.0.1` (localhost) by default — not accessible from the internet.
- The token is sent via DM, never in a public channel.
- Run `[p]dashboard newtoken` to rotate your token anytime.
- If you need remote access (bot on a VPS), consider SSH tunneling rather than exposing the port publicly.

---

## ⚙️ Configuration

Default port: **8765**  
Default host: **127.0.0.1**

Change port:
```
[p]dashboard port 9000
[p]reload redbot_dashboard
```

---

## 🐛 Troubleshooting

**"Connection refused"**
- Make sure the cog is loaded: `[p]load redbot_dashboard`
- Check the port matches: `[p]dashboard status`
- Firewall blocking localhost? Unlikely but check.

**"Unauthorized"**
- Token mismatch — regenerate with `[p]dashboard newtoken` and reconnect.

**No CPU/RAM data**
- Install psutil: `pip install psutil` then `[p]reload redbot_dashboard`

**Anime.js import error in dev**
- Run `npm install` first in the `electron-app/` directory.

---

## 📄 License

MIT — Use freely, modify as needed.
