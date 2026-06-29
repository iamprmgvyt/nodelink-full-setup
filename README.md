# 🎵 NodeLink Full Setup — Production-Ready `config.js`

> A clean, fully-commented `config.js` for [NodeLink](https://github.com/PerformanC/NodeLink), stripped of all personal credentials and ready to deploy on any hosting environment.

This repo provides a drop-in `config.js` that works out of the box with the official NodeLink source. Optimized for **clustered playback**, **low-resource VPS**, and **panel-based hosting** (Pterodactyl, HidenCloud, etc.). Supports 40+ audio sources with all filters enabled.

---

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
  - [Method A — Use This Repo (Recommended)](#method-a--use-this-repo-recommended)
  - [Method B — Official NodeLink + Rename Config](#method-b--official-nodelink--rename-config)
- [Required Configuration](#-required-configuration)
  - [1. Server Port & Password](#1-server-port--password)
  - [2. YouTube Refresh Token](#2-youtube-refresh-token-prevents-http-429)
  - [3. Spotify Credentials](#3-spotify-credentials)
- [Optional Sources](#-optional-sources)
- [How to Run](#-how-to-run)
  - [Option A — Standard VPS / Linux](#option-a--standard-vps--linux)
  - [Option B — Pterodactyl / Web Panels](#option-b--pterodactyl--web-panels)
- [Connecting Your Discord Bot](#-connecting-your-discord-bot)
- [Cluster & Performance Tuning](#-cluster--performance-tuning)

---

## 🛑 Prerequisites

| Requirement | Detail |
|---|---|
| **Node.js** | **v22.x or higher** — strictly required |
| **npm** | Bundled with Node.js |

Verify your Node.js version before continuing:
```bash
node -v
# Must output v22.x.x or higher
```

---

## 📦 Installation

### Method A — Use This Repo (Recommended)

Clone the **official NodeLink** source, then drop in this repo's `config.js`.

```bash
# Step 1 — Clone the official NodeLink source
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# Step 2 — Download this repo's config.js into the NodeLink folder
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js

# Step 3 — Open config.js and fill in your credentials (see below)

# Step 4 — Install and run
npm install
npm run start
```

Alternatively, clone this entire repo and copy `config.js` manually:

```bash
git clone https://github.com/iamprmgvyt/nodelink-full-setup.git
# Then copy config.js into your NodeLink folder
```

---

### Method B — Official NodeLink + Rename Config

The official NodeLink repo ships a `config.default.js` but no `config.js`. You can rename and use it:

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# Rename the default config
cp config.default.js config.js

# Open config.js and fill in your credentials
npm install
npm run start
```

> ⚠️ `config.default.js` may be missing newer fields. Using this repo's `config.js` (Method A) is recommended for full feature support.

---

## 🔑 Required Configuration

Open `config.js` in any text editor. You **must** fill in these 3 sections before NodeLink will work correctly.

---

### 1. Server Port & Password

Located at the very top of `config.js`:

```javascript
server: {
  host: '0.0.0.0',
  port: yourporthere,       // ← Replace with your actual port number
  password: 'yourpassword', // ← Replace with a strong password
  useBunServer: false
}
```

**What port to use?**
- **VPS with root access** — use any open port, e.g. `2333`
- **Pterodactyl / HidenCloud panel** — use the port assigned by your panel, found in the **Network** tab of your server

---

### 2. YouTube Refresh Token (Prevents HTTP 429)

Without this, YouTube will block playback with rate-limit errors after a few requests. This token authenticates NodeLink as a TV client and bypasses most restrictions.

#### Step-by-step: Get a YouTube Refresh Token

> Uses Google's official device OAuth flow — no third-party tools required.

**Step 1 — Request a device code**

Run in terminal (or use Postman/Insomnia):

```bash
curl -X POST "https://oauth2.googleapis.com/device/code" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&scope=https://www.googleapis.com/auth/youtube"
```

Response:

```json
{
  "device_code": "AH-1Ng3f...",
  "user_code": "ABCD-EFGH",
  "verification_url": "https://www.google.com/device",
  "expires_in": 1800,
  "interval": 5
}
```

**Step 2 — Authorize via browser**

1. Open [https://www.google.com/device](https://www.google.com/device)
2. Enter the `user_code` (e.g. `ABCD-EFGH`)
3. Sign in with your Google account and click **Allow**

> 💡 Use a **dedicated Google account**, not your personal one.

**Step 3 — Exchange for tokens**

Replace `DEVICE_CODE_HERE` with the `device_code` from Step 1:

```bash
curl -X POST "https://oauth2.googleapis.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&client_secret=SboVhoG9s0rNafixCSGGKXAT&code=DEVICE_CODE_HERE&grant_type=http://oauth.net/grant_type/device/1.0"
```

Response:

```json
{
  "access_token": "ya29.xxx",
  "refresh_token": "1//0eXXXXXXXXXXXX",
  "token_type": "Bearer",
  "expires_in": 3599
}
```

**Step 4 — Paste `refresh_token` into `config.js`**

```javascript
clients: {
  settings: {
    TV: {
      refreshToken: ['1//0eXXXXXXXXXXXX']
      // Multiple tokens rotate automatically on failure:
      // refreshToken: ['token1', 'token2']
    }
  }
}
```

> ✅ Refresh tokens do **not expire** unless manually revoked — you only need to do this once.

---

### 3. Spotify Credentials

Required for Spotify search, metadata, and playlist/album loading. Audio is streamed via YouTube or fallback sources.

#### Step-by-step: Get Spotify Client ID & Secret

1. Go to [https://developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Log in with your Spotify account
3. Click **Create App** and fill in:
   - **App Name:** anything (e.g. `NodeLink`)
   - **Redirect URI:** `http://localhost`
   - Check **Web API**
4. Click **Save**, then open the app and go to **Settings**
5. Copy **Client ID** and **Client Secret**

Paste into `config.js`:

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',        // ← Paste here
  clientSecret: 'YOUR_CLIENT_SECRET_HERE', // ← Paste here
  market: 'US',
  allowExplicit: true
}
```

> ⚠️ Never commit real credentials to a public GitHub repository.

---

## 🎧 Optional Sources

All sources below are already enabled in `config.js`. These only need credentials for premium/higher-quality access.

<details>
<summary><strong>Apple Music</strong></summary>

Auto-fetches token by default. Leave `token_here` as-is unless you have a manual token.

```javascript
applemusic: {
  enabled: true,
  mediaApiToken: 'token_here', // 'token_here' = auto-fetch
  market: 'US'
}
```
</details>

<details>
<summary><strong>Tidal</strong></summary>

Auto-fetches via Google login. Leave `token_here` for auto. For lossless, see [hifi-api](https://github.com/binimum/hifi-api/).

```javascript
tidal: {
  enabled: true,
  token: 'token_here', // 'token_here' = auto-fetch
  countryCode: 'US'
}
```
</details>

<details>
<summary><strong>Deezer</strong> — ARL cookie for premium/lossless</summary>

1. Open [deezer.com](https://www.deezer.com) and log in
2. Open DevTools → Application → Cookies → copy the `arl` value

```javascript
deezer: {
  enabled: true,
  // arl: 'YOUR_ARL_COOKIE',
}
```
</details>

<details>
<summary><strong>Qobuz</strong> — user token for 320kbps/FLAC</summary>

1. Open [play.qobuz.com](https://play.qobuz.com) and log in
2. DevTools → Application → Local Storage → `localuser` → `token`

```javascript
qobuz: {
  enabled: true,
  userToken: 'YOUR_TOKEN',
  formatId: '5'  // 5 = MP3 320 | 6 = FLAC | 27 = Hi-Res FLAC
}
```
</details>

<details>
<summary><strong>VK Music</strong></summary>

Open [vk.com](https://vk.com) → DevTools → Network → find POST `/?act=web_token`
- **userToken:** from Response → `access_token`
- **userCookie:** from Request Headers → `cookie` (full value)

```javascript
vkmusic: {
  enabled: true,
  userToken: '',  // Option 1
  userCookie: '' // Option 2
}
```
</details>

<details>
<summary><strong>Yandex Music</strong></summary>

```javascript
yandexmusic: {
  enabled: true,
  accessToken: 'YOUR_YANDEX_TOKEN'
}
```
</details>

<details>
<summary><strong>Bilibili</strong> — SESSDATA for 4K+/premium</summary>

1. Open [bilibili.com](https://www.bilibili.com) → DevTools → Application → Cookies → `SESSDATA`

```javascript
bilibili: {
  enabled: true,
  sessdata: 'YOUR_SESSDATA'
}
```
</details>

<details>
<summary><strong>Audius</strong> — API key for higher rate limits</summary>

1. Go to [https://audius.co/settings](https://audius.co/settings) → Create an App

```javascript
audius: {
  enabled: true,
  appName: 'YourAppName',
  apiKey: 'YOUR_API_KEY',
  apiSecret: 'YOUR_API_SECRET'
}
```
</details>

<details>
<summary><strong>Last.fm</strong> — for lyrics/metadata</summary>

Get your key at [https://www.last.fm/api/account/create](https://www.last.fm/api/account/create)

```javascript
lastfm: {
  enabled: true,
  apiKey: 'YOUR_LASTFM_API_KEY'
}
```
</details>

<details>
<summary><strong>SongLink / Odesli</strong> — cross-platform track resolution</summary>

API key optional. Get at [https://odesli.co/](https://odesli.co/)

```javascript
songlink: {
  enabled: true,
  apiKey: '' // Optional
}
```
</details>

---

## ▶️ How to Run

### Option A — Standard VPS / Linux

```bash
npm install
npm run start

# Keep alive 24/7 with PM2
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save
pm2 startup   # Auto-restart on server reboot
```

---

### Option B — Pterodactyl / Web Panels

Panels enforce a **16-character limit** on startup file paths. NodeLink's default entry `dist/src/index.js` is 18 characters and will be rejected.

**Fix — create a `run.js` relay file:**

**1.** Create `run.js` in your server root directory with this content:

```javascript
import './dist/src/index.js';
```

**2.** In your panel → **Startup** settings tab → change **Startup File** to:

```
run.js
```

**3.** Click **Start** from the Console tab. The panel handles `npm install` automatically.

> ✅ No SSH access needed.

---

## 🤖 Connecting Your Discord Bot

Once the console shows:

```
[STARTED] >: Server > Successfully listening on host 0.0.0.0, port XXXX
```

Add NodeLink to your music client (Riffy, Shoukaku, Poru, Erela.js, etc.):

```javascript
const nodes = [
  {
    name: 'MyNodeLink',
    host: 'your.server.domain.com',  // VPS IP/domain or panel hostname
    port: 2333,                       // Must match config.js → server.port
    password: 'your_password',        // Must match config.js → server.password
    secure: false                     // true ONLY if your host provides SSL on this port
  }
];
```

> **Panel users:** Your hostname is in the panel's Network or Connection tab (e.g. `node1.hidencloud.com`)

---

## ⚙️ Cluster & Performance Tuning

```javascript
cluster: {
  enabled: true,
  workers: 0,       // 0 = auto (1 per CPU core). Set to 1 on tiny VPS
  minWorkers: 1,
  specializedSourceWorker: {
    enabled: true,
    count: 1,
    microWorkers: 2  // Increase on servers with 4+ cores
  },
  hibernation: {
    enabled: true,
    timeoutMs: 1200000  // Workers sleep after 20 min idle — saves RAM
  }
}
```

**Recommended by RAM:**

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 MB | `1` | `1` |
| 1 GB | `0` (auto) | `2` |
| 2 GB+ | `0` (auto) | `2–4` |

---

## 📝 Notes

- Never commit real credentials to a public repo — fork this repo privately if needed
- NodeLink is developed by [PerformanC](https://github.com/PerformanC/NodeLink) — check their repo for upstream updates
- This setup is maintained at [iamprmgvyt/nodelink-full-setup](https://github.com/iamprmgvyt/nodelink-full-setup)

 - Built with ♥️
