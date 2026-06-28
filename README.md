# 🎵 NodeLink — Production-Ready `config.js`

> A clean, fully-commented `config.js` template for [NodeLink](https://github.com/PerformanC/NodeLink), stripped of all personal credentials and ready to deploy.

This configuration is optimized for **clustered playback**, **low-resource hosting**, and **panel-based environments** (Pterodactyl, HidenCloud, etc.). It ships with support for 40+ audio sources and all audio filters enabled.

---

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Required Configuration](#-required-configuration)
  - [1. Server Settings](#1-server-settings)
  - [2. YouTube Refresh Token](#2-youtube-refresh-token)
  - [3. Spotify Credentials](#3-spotify-credentials)
- [Optional Sources](#-optional-sources)
  - [Apple Music](#apple-music)
  - [Tidal](#tidal)
  - [Deezer](#deezer)
  - [Qobuz](#qobuz)
  - [Yandex Music](#yandex-music)
  - [VK Music](#vk-music)
  - [Bilibili](#bilibili)
  - [Audius](#audius)
  - [Last.fm](#lastfm)
  - [SongLink / Odesli](#songlink--odesli)
- [How to Run](#-how-to-run)
  - [Option A — Standard VPS / Linux](#option-a--standard-vps--linux)
  - [Option B — Pterodactyl / Web Panels](#option-b--pterodactyl--web-panels)
- [Connecting Your Discord Bot](#-connecting-your-discord-bot)
- [Cluster & Performance Tuning](#-cluster--performance-tuning)

---

## 🛑 Prerequisites

| Requirement | Version |
|---|---|
| **Node.js** | **22.x or higher** (strictly required) |
| **npm** | Included with Node.js |

> ⚠️ NodeLink will **not** start on Node.js versions below 22. Verify with `node -v` before proceeding.

---

## ⚡ Quick Start

```bash
# 1. Clone the official NodeLink repository
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# 2. Download this config.js and place it in the root folder
#    (replace the existing config.js)

# 3. Open config.js and fill in your credentials (see below)

# 4. Install dependencies
npm install

# 5. Start the server
npm run start
```

---

## 🔑 Required Configuration

Open `config.js` and replace the following placeholders before starting:

### 1. Server Settings

Located at the **very top** of the file:

```javascript
server: {
  host: '0.0.0.0',
  port: yourporthere,        // ← Replace with your allocated port (e.g. 2333, 24656)
  password: 'yourpassword',  // ← Replace with a strong password
  useBunServer: false
}
```

> **Panel users:** Use the port assigned to you in the Pterodactyl/HidenCloud panel. Do **not** use `2333` unless it is explicitly allocated to you.

---

### 2. YouTube Refresh Token

Without this token, YouTube playback will frequently hit **rate limit errors (HTTP 429)**.

**How to get your token:** Follow the [NodeLink YouTube Token Guide](https://github.com/PerformanC/NodeLink/blob/main/USING.md) (TV client OAuth flow).

Once you have it, find the `clients.settings` block:

```javascript
clients: {
  settings: {
    TV: {
      refreshToken: ['<yourtokenhere>']
      // You can also use multiple tokens for rotation:
      // refreshToken: ['token1', 'token2']
    }
  }
}
```

---

### 3. Spotify Credentials

Required for Spotify track **search and metadata resolution**. Actual audio is streamed via YouTube/fallback sources.

**How to get credentials:**
1. Go to [Spotify Developer Dashboard](https://developer.spotify.com/dashboard)
2. Create a new application
3. Copy the **Client ID** and **Client Secret**

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_SPOTIFY_CLIENT_ID',        // ← Replace
  clientSecret: 'YOUR_SPOTIFY_CLIENT_SECRET', // ← Replace
  externalAuthUrl: '',  // Optional external token provider URL
  market: 'US',
  allowExplicit: true,
  // ... rest can stay as default
}
```

---

## 🎧 Optional Sources

These sources work out-of-the-box without credentials. The ones listed below require extra setup for full functionality.

### Apple Music

Auto-fetches a token by default. For manual token:

```javascript
applemusic: {
  enabled: true,
  mediaApiToken: 'token_here', // Leave as 'token_here' for auto-fetch
  market: 'US'
}
```

---

### Tidal

Token auto-fetches via Google login. For manual token:

```javascript
tidal: {
  enabled: true,
  token: 'token_here', // Leave as 'token_here' for auto-fetch
  countryCode: 'US'
}
```

For **HiFi/lossless streaming**, set up a [hifi-api](https://github.com/binimum/hifi-api/) instance and add it to `hifiApis`.

---

### Deezer

Works without credentials. For premium/lossless content, provide an ARL cookie:

```javascript
deezer: {
  enabled: true,
  // arl: 'YOUR_DEEZER_ARL_COOKIE', // Uncomment and fill for premium
  // decryptionKey: '',              // Required alongside ARL for lossless
}
```

---

### Qobuz

Works without credentials for low-quality streams. For 320kbps/FLAC:

```javascript
qobuz: {
  enabled: true,
  userToken: 'YOUR_QOBUZ_TOKEN', // Get from play.qobuz.com → DevTools → LocalStorage → localuser → token
  formatId: '5'  // 5 = MP3 320kbps | 6 = FLAC | 27 = Hi-Res FLAC
}
```

---

### Yandex Music

Requires an access token for playback (Yandex Music is region-restricted):

```javascript
yandexmusic: {
  enabled: true,
  accessToken: 'YOUR_YANDEX_TOKEN' // Get from Yandex Music API
}
```

---

### VK Music

Two authentication methods (choose one):

```javascript
vkmusic: {
  enabled: true,
  userToken: '',  // From browser DevTools → POST /?act=web_token → response → access_token
  userCookie: '' // OR: same request → request headers → cookie (full header value)
}
```

---

### Bilibili

Works without credentials. For premium/4K+ content:

```javascript
bilibili: {
  enabled: true,
  sessdata: 'YOUR_SESSDATA' // Get from bilibili.com → DevTools → Application → Cookies → SESSDATA
}
```

---

### Audius

Free and open. For higher rate limits, register an app:

```javascript
audius: {
  enabled: true,
  appName: 'YourAppName',   // From https://audius.co/settings → Create App
  apiKey: 'YOUR_API_KEY',
  apiSecret: 'YOUR_API_SECRET'
}
```

---

### Last.fm

Used for lyrics and metadata enrichment:

```javascript
lastfm: {
  enabled: true,
  apiKey: 'YOUR_LASTFM_API_KEY' // Get from https://www.last.fm/api/account/create
}
```

---

### SongLink / Odesli

Cross-platform track resolution (finds the same song across streaming platforms):

```javascript
songlink: {
  enabled: true,
  apiKey: '', // Optional — get from https://odesli.co/
  userCountry: 'US'
}
```

---

## ▶️ How to Run

### Option A — Standard VPS / Linux

```bash
# Install dependencies
npm install

# Start NodeLink
npm run start

# (Recommended) Run 24/7 in background with PM2
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save
pm2 startup  # Auto-start on server reboot
```

---

### Option B — Pterodactyl / Web Panels

Web panels often reject startup file paths longer than **16 characters**. The default NodeLink entry point (`dist/src/index.js`) is 18 characters and will fail.

**Fix — create a `run.js` relay file:**

1. Create a new file in your server's **root directory** named exactly `run.js`
2. Paste this single line into it:

```javascript
import './dist/src/index.js';
```

3. Save the file.
4. In your panel, go to **Startup** settings.
5. Change the **Startup File** field from `dist/src/index.js` to:

```
run.js
```

6. Click **Start** from the Console tab. The panel will run `npm install` and launch automatically.

> ✅ The panel's own `npm install` step will handle dependencies — no SSH needed.

---

## 🤖 Connecting Your Discord Bot

Once your NodeLink console prints:

```
[STARTED] >: Server > Successfully listening on host 0.0.0.0, port XXXX
```

Configure your bot's music client (Riffy, Shoukaku, Poru, Erela.js, etc.):

```javascript
const nodes = [
  {
    name: 'MyNodeLink',
    host: 'your.server.domain.com',  // Your VPS domain or panel hostname
    port: 2333,                       // Must match config.js → server.port
    password: 'your_password',        // Must match config.js → server.password
    secure: false                     // Set to true ONLY if your host provides SSL on this port
  }
];
```

> ℹ️ **Panel users:** Your hostname is typically something like `node1.hidencloud.com`. Check your panel's connection info tab.

---

## ⚙️ Cluster & Performance Tuning

This config ships with clustering enabled and sensible defaults for most VPS tiers. Key values to adjust based on your hardware:

```javascript
cluster: {
  enabled: true,
  workers: 0,           // 0 = auto (uses all CPU cores). Set to 1 for tiny VPS (512MB RAM)
  minWorkers: 1,
  specializedSourceWorker: {
    enabled: true,
    count: 1,           // Increase on multi-core servers
    microWorkers: 2     // Threads per process cluster
  },
  hibernation: {
    enabled: true,
    timeoutMs: 1200000  // Workers sleep after 20 min of inactivity (saves RAM)
  }
}
```

**RAM guidance:**

| RAM | Recommended `workers` setting |
|---|---|
| 512 MB | `1` |
| 1 GB | `0` (auto, ~2 cores) |
| 2 GB+ | `0` (auto) |

---

## 📝 License

This configuration template is provided as-is for community use. NodeLink itself is licensed under its own terms — see the [official repository](https://github.com/PerformanC/NodeLink).
