# 🎵 NodeLink Full Setup — Production-Ready `config.js`

<div align="center">

### 🌐 Language / Ngôn ngữ / Langue

[🇬🇧 English](#-english) &nbsp;|&nbsp; [🇻🇳 Tiếng Việt](#-tiếng-việt) &nbsp;|&nbsp; [🇫🇷 Français](#-français)

</div>

---

<br/>

# 🇬🇧 English

> A clean, fully-commented `config.js` for [NodeLink](https://github.com/PerformanC/NodeLink), stripped of all personal credentials and ready to deploy on any hosting environment.

Optimized for **clustered playback**, **low-resource VPS**, and **panel-based hosting** (Pterodactyl, HidenCloud, etc.). Supports 40+ audio sources with all audio filters enabled.

## 📋 Table of Contents

- [Prerequisites](#-prerequisites)
- [Installation](#-installation-en)
  - [Method A — Use This Repo (Recommended)](#method-a--use-this-repo-recommended)
  - [Method B — Official NodeLink + Rename Config](#method-b--official-nodelink--rename-config)
- [Required Configuration](#-required-configuration)
  - [1. Server Port & Password](#1-server-port--password)
  - [2. YouTube Refresh Token](#2-youtube-refresh-token)
  - [3. Spotify Credentials](#3-spotify-credentials)
- [Optional Sources](#-optional-sources-en)
- [How to Run](#-how-to-run-en)
  - [Option A — Standard VPS / Linux](#option-a--standard-vps--linux-en)
  - [Option B — Pterodactyl / Web Panels](#option-b--pterodactyl--web-panels-en)
- [Connecting Your Discord Bot](#-connecting-your-discord-bot-en)
- [Cluster & Performance Tuning](#-cluster--performance-tuning-en)

---

## 🛑 Prerequisites

| Requirement | Detail |
|---|---|
| **Node.js** | **v22.x or higher** — strictly required |
| **npm** | Bundled with Node.js |

```bash
node -v
# Must output v22.x.x or higher
```

---

## 📦 Installation {#-installation-en}

### Method A — Use This Repo (Recommended)

```bash
# 1 — Clone the official NodeLink source
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# 2 — Download this repo's config.js
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js

# 3 — Fill in your credentials (see below), then:
npm install
npm run start
```

Or clone this entire repo and copy `config.js` manually:

```bash
git clone https://github.com/iamprmgvyt/nodelink-full-setup.git
# Copy config.js into your NodeLink folder
```

---

### Method B — Official NodeLink + Rename Config

The official NodeLink repo ships `config.default.js` — no `config.js` included. Rename and use it:

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
cp config.default.js config.js
npm install
npm run start
```

> ⚠️ `config.default.js` may be missing newer fields. Method A is recommended for full feature support.

---

## 🔑 Required Configuration

### 1. Server Port & Password

```javascript
server: {
  host: '0.0.0.0',
  port: yourporthere,       // ← Your allocated port (e.g. 2333)
  password: 'yourpassword', // ← A strong password
  useBunServer: false
}
```

- **VPS:** use any open port, e.g. `2333`
- **Panel (Pterodactyl / HidenCloud):** use the port shown in your server's **Network** tab

---

### 2. YouTube Refresh Token

Without this, YouTube returns **HTTP 429 rate-limit errors** after a few tracks.

#### How to get it (Google Device OAuth — no third-party tools)

**Step 1 — Request a device code**

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
  "expires_in": 1800
}
```

**Step 2 — Authorize**

1. Open [https://www.google.com/device](https://www.google.com/device)
2. Enter the `user_code` (e.g. `ABCD-EFGH`)
3. Sign in with a **dedicated Google account** and click **Allow**

**Step 3 — Exchange for refresh token**

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
  "expires_in": 3599
}
```

**Step 4 — Paste into `config.js`**

```javascript
clients: {
  settings: {
    TV: {
      refreshToken: ['1//0eXXXXXXXXXXXX']
      // Multiple tokens rotate automatically:
      // refreshToken: ['token1', 'token2']
    }
  }
}
```

> ✅ Refresh tokens **do not expire** unless manually revoked.

---

### 3. Spotify Credentials

Required for Spotify search and playlist loading. Audio streams via YouTube/fallback.

**How to get Client ID & Secret:**

1. Go to [https://developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Log in → **Create App**
3. Fill in App Name, Redirect URI: `http://localhost`, check **Web API** → **Save**
4. Open the app → **Settings** → copy **Client ID** and **Client Secret**

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',
  clientSecret: 'YOUR_CLIENT_SECRET_HERE',
  market: 'US',
  allowExplicit: true
}
```

---

## 🎧 Optional Sources {#-optional-sources-en}

<details>
<summary><strong>Apple Music</strong> — auto-fetches token by default</summary>

Leave `'token_here'` as-is for automatic token fetching.

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
</details>

<details>
<summary><strong>Tidal</strong> — auto-fetches via Google login</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
For lossless streaming, set up [hifi-api](https://github.com/binimum/hifi-api/) and add to `hifiApis`.
</details>

<details>
<summary><strong>Deezer</strong> — ARL cookie for premium/lossless</summary>

deezer.com → DevTools → Application → Cookies → copy `arl`

```javascript
deezer: { enabled: true, /* arl: 'YOUR_ARL' */ }
```
</details>

<details>
<summary><strong>Qobuz</strong> — user token for 320kbps / FLAC</summary>

play.qobuz.com → DevTools → Application → LocalStorage → `localuser` → `token`

```javascript
qobuz: { enabled: true, userToken: 'YOUR_TOKEN', formatId: '5' }
// formatId: '5' = MP3 320kbps | '6' = FLAC | '27' = Hi-Res FLAC
```
</details>

<details>
<summary><strong>VK Music</strong></summary>

vk.com → DevTools → Network → POST `/?act=web_token`
- `userToken` → Response → `access_token`
- `userCookie` → Request Headers → `cookie`

```javascript
vkmusic: { enabled: true, userToken: '', userCookie: '' }
```
</details>

<details>
<summary><strong>Bilibili</strong> — SESSDATA for 4K+/premium</summary>

bilibili.com → DevTools → Application → Cookies → `SESSDATA`

```javascript
bilibili: { enabled: true, sessdata: 'YOUR_SESSDATA' }
```
</details>

<details>
<summary><strong>Audius</strong> — API key for higher rate limits</summary>

[https://audius.co/settings](https://audius.co/settings) → Create an App

```javascript
audius: { enabled: true, appName: '', apiKey: '', apiSecret: '' }
```
</details>

<details>
<summary><strong>Last.fm</strong> — lyrics & metadata</summary>

[https://www.last.fm/api/account/create](https://www.last.fm/api/account/create)

```javascript
lastfm: { enabled: true, apiKey: 'YOUR_API_KEY' }
```
</details>

---

## ▶️ How to Run {#-how-to-run-en}

### Option A — Standard VPS / Linux {#option-a--standard-vps--linux-en}

```bash
npm install && npm run start

# 24/7 with PM2
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

### Option B — Pterodactyl / Web Panels {#option-b--pterodactyl--web-panels-en}

Panels enforce a **16-character limit** on startup file paths. `dist/src/index.js` = 18 chars → rejected.

**Fix:** Create `run.js` in your server root:

```javascript
import './dist/src/index.js';
```

Then in your panel → **Startup** tab → set **Startup File** to `run.js` → click **Start**.

---

## 🤖 Connecting Your Discord Bot {#-connecting-your-discord-bot-en}

When the console shows `Successfully listening on host 0.0.0.0, port XXXX`:

```javascript
const nodes = [{
  name: 'MyNodeLink',
  host: 'your.server.domain.com', // VPS IP/domain or panel hostname
  port: 2333,                      // Must match config.js → server.port
  password: 'your_password',       // Must match config.js → server.password
  secure: false                    // true ONLY if host provides SSL on this port
}];
```

---

## ⚙️ Cluster & Performance Tuning {#-cluster--performance-tuning-en}

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 MB | `1` | `1` |
| 1 GB | `0` (auto) | `2` |
| 2 GB+ | `0` (auto) | `2–4` |

```javascript
cluster: {
  workers: 0,        // 0 = 1 worker per CPU core
  hibernation: { enabled: true, timeoutMs: 1200000 } // Sleep after 20 min idle
}
```

---

<br/>

---

<br/>

# 🇻🇳 Tiếng Việt

> File `config.js` sạch, đầy đủ comment cho [NodeLink](https://github.com/PerformanC/NodeLink), đã xóa toàn bộ credential cá nhân, sẵn sàng deploy trên mọi môi trường hosting.

Tối ưu cho **phát nhạc đa luồng (cluster)**, **VPS ít RAM**, và **panel hosting** (Pterodactyl, HidenCloud,...). Hỗ trợ 40+ nguồn âm thanh với đầy đủ bộ lọc âm thanh.

## 📋 Mục Lục

- [Yêu Cầu](#-yêu-cầu)
- [Cài Đặt](#-cài-đặt)
  - [Cách A — Dùng Repo Này (Khuyến Nghị)](#cách-a--dùng-repo-này-khuyến-nghị)
  - [Cách B — NodeLink Gốc + Đổi Tên Config](#cách-b--nodelink-gốc--đổi-tên-config)
- [Cấu Hình Bắt Buộc](#-cấu-hình-bắt-buộc)
  - [1. Port & Mật Khẩu Server](#1-port--mật-khẩu-server)
  - [2. YouTube Refresh Token](#2-youtube-refresh-token-vn)
  - [3. Spotify Credentials](#3-spotify-credentials-vn)
- [Nguồn Nhạc Tùy Chọn](#-nguồn-nhạc-tùy-chọn)
- [Cách Chạy](#-cách-chạy)
  - [Cách A — VPS / Linux thông thường](#cách-a--vps--linux-thông-thường)
  - [Cách B — Pterodactyl / Web Panel](#cách-b--pterodactyl--web-panel)
- [Kết Nối Discord Bot](#-kết-nối-discord-bot)
- [Tùy Chỉnh Cluster & Hiệu Năng](#-tùy-chỉnh-cluster--hiệu-năng)

---

## 🛑 Yêu Cầu

| Yêu cầu | Chi tiết |
|---|---|
| **Node.js** | **v22.x trở lên** — bắt buộc |
| **npm** | Đi kèm với Node.js |

```bash
node -v
# Phải ra v22.x.x trở lên
```

---

## 📦 Cài Đặt

### Cách A — Dùng Repo Này (Khuyến Nghị)

```bash
# 1 — Clone NodeLink gốc từ PerformanC
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# 2 — Tải config.js từ repo này vào thư mục NodeLink
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js

# 3 — Mở config.js, điền thông tin của bạn (xem bên dưới), rồi:
npm install
npm run start
```

Hoặc clone cả repo này rồi copy `config.js` thủ công:

```bash
git clone https://github.com/iamprmgvyt/nodelink-full-setup.git
# Copy config.js vào thư mục NodeLink của bạn
```

---

### Cách B — NodeLink Gốc + Đổi Tên Config

NodeLink gốc chỉ có `config.default.js`, không có `config.js`. Bạn đổi tên và dùng luôn:

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
cp config.default.js config.js
npm install
npm run start
```

> ⚠️ `config.default.js` có thể thiếu các field mới. Nên dùng Cách A để có đầy đủ tính năng.

---

## 🔑 Cấu Hình Bắt Buộc

### 1. Port & Mật Khẩu Server

Nằm ngay đầu file `config.js`:

```javascript
server: {
  host: '0.0.0.0',
  port: yourporthere,       // ← Điền port của bạn (vd: 2333)
  password: 'yourpassword', // ← Đặt mật khẩu mạnh
  useBunServer: false
}
```

- **VPS tự quản lý:** dùng bất kỳ port nào còn trống, ví dụ `2333`
- **Panel (Pterodactyl / HidenCloud):** dùng port được panel cấp, xem trong tab **Network** của server

---

### 2. YouTube Refresh Token {#2-youtube-refresh-token-vn}

Không có token này, YouTube sẽ chặn phát nhạc với lỗi **HTTP 429 (rate limit)** sau vài bài.

#### Cách lấy (Google Device OAuth — không cần tool bên thứ 3)

**Bước 1 — Lấy device code**

Chạy trong terminal (hoặc dùng Postman/Insomnia):

```bash
curl -X POST "https://oauth2.googleapis.com/device/code" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&scope=https://www.googleapis.com/auth/youtube"
```

Response trả về:
```json
{
  "device_code": "AH-1Ng3f...",
  "user_code": "ABCD-EFGH",
  "verification_url": "https://www.google.com/device",
  "expires_in": 1800
}
```

**Bước 2 — Xác thực trên trình duyệt**

1. Mở [https://www.google.com/device](https://www.google.com/device)
2. Nhập `user_code` (ví dụ: `ABCD-EFGH`)
3. Đăng nhập bằng Google và nhấn **Allow**

> 💡 Nên dùng **tài khoản Google riêng**, không dùng tài khoản cá nhân.

**Bước 3 — Đổi lấy refresh token**

Thay `DEVICE_CODE_HERE` bằng `device_code` ở Bước 1:

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
  "expires_in": 3599
}
```

**Bước 4 — Dán vào `config.js`**

```javascript
clients: {
  settings: {
    TV: {
      refreshToken: ['1//0eXXXXXXXXXXXX']
      // Nhiều token sẽ tự động xoay vòng khi lỗi:
      // refreshToken: ['token1', 'token2']
    }
  }
}
```

> ✅ Refresh token **không hết hạn** trừ khi bạn tự thu hồi — chỉ cần làm một lần.

---

### 3. Spotify Credentials {#3-spotify-credentials-vn}

Cần thiết để tìm kiếm, load playlist/album từ Spotify. Âm thanh thực tế được stream qua YouTube hoặc nguồn fallback.

**Cách lấy Client ID & Client Secret:**

1. Vào [https://developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Đăng nhập → nhấn **Create App**
3. Điền:
   - **App Name:** tùy ý (vd: `NodeLink`)
   - **Redirect URI:** `http://localhost`
   - Tích chọn **Web API**
4. Nhấn **Save** → mở app vừa tạo → **Settings**
5. Copy **Client ID** và **Client Secret**

Dán vào `config.js`:

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',
  clientSecret: 'YOUR_CLIENT_SECRET_HERE',
  market: 'US',
  allowExplicit: true
}
```

> ⚠️ Không commit credential thật lên GitHub public.

---

## 🎧 Nguồn Nhạc Tùy Chọn

Tất cả đã được bật sẵn trong `config.js`. Chỉ cần credential khi muốn truy cập nội dung premium hoặc chất lượng cao hơn.

<details>
<summary><strong>Apple Music</strong> — tự lấy token, không cần làm gì</summary>

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
Để nguyên `'token_here'` — NodeLink sẽ tự fetch token.
</details>

<details>
<summary><strong>Tidal</strong> — tự đăng nhập qua Google</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
Muốn lossless: cài [hifi-api](https://github.com/binimum/hifi-api/) rồi thêm vào `hifiApis`.
</details>

<details>
<summary><strong>Deezer</strong> — cần ARL cookie cho premium/lossless</summary>

Vào deezer.com (đã đăng nhập) → F12 → Application → Cookies → copy giá trị `arl`

```javascript
deezer: { enabled: true, /* arl: 'YOUR_ARL' */ }
```
</details>

<details>
<summary><strong>Qobuz</strong> — cần token cho 320kbps / FLAC</summary>

play.qobuz.com → F12 → Application → Local Storage → `localuser` → `token`

```javascript
qobuz: { enabled: true, userToken: 'YOUR_TOKEN', formatId: '5' }
// '5' = MP3 320kbps | '6' = FLAC | '27' = Hi-Res FLAC
```
</details>

<details>
<summary><strong>VK Music</strong></summary>

vk.com → F12 → Network → tìm POST `/?act=web_token`
- `userToken`: Response → `access_token`
- `userCookie`: Request Headers → `cookie` (copy toàn bộ)

```javascript
vkmusic: { enabled: true, userToken: '', userCookie: '' }
```
</details>

<details>
<summary><strong>Bilibili</strong> — cần SESSDATA cho 4K+/premium</summary>

bilibili.com → F12 → Application → Cookies → `SESSDATA`

```javascript
bilibili: { enabled: true, sessdata: 'YOUR_SESSDATA' }
```
</details>

<details>
<summary><strong>Audius</strong> — API key tăng rate limit</summary>

[https://audius.co/settings](https://audius.co/settings) → Create an App

```javascript
audius: { enabled: true, appName: '', apiKey: '', apiSecret: '' }
```
</details>

<details>
<summary><strong>Last.fm</strong> — lyrics & metadata</summary>

[https://www.last.fm/api/account/create](https://www.last.fm/api/account/create)

```javascript
lastfm: { enabled: true, apiKey: 'YOUR_API_KEY' }
```
</details>

---

## ▶️ Cách Chạy

### Cách A — VPS / Linux thông thường

```bash
npm install && npm run start

# Chạy nền 24/7 với PM2
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

### Cách B — Pterodactyl / Web Panel

Panel giới hạn **16 ký tự** cho đường dẫn file khởi động. `dist/src/index.js` = 18 ký tự → bị từ chối.

**Cách fix:** Tạo file `run.js` trong thư mục gốc server:

```javascript
import './dist/src/index.js';
```

Vào panel → tab **Startup** → đổi **Startup File** thành `run.js` → nhấn **Start**.

> ✅ Không cần SSH — panel tự chạy `npm install`.

---

## 🤖 Kết Nối Discord Bot

Khi console hiển thị `Successfully listening on host 0.0.0.0, port XXXX`:

```javascript
const nodes = [{
  name: 'MyNodeLink',
  host: 'your.server.domain.com', // IP/domain VPS hoặc hostname của panel
  port: 2333,                      // Phải khớp với server.port trong config.js
  password: 'your_password',       // Phải khớp với server.password trong config.js
  secure: false                    // true chỉ khi host cung cấp SSL trên port này
}];
```

---

## ⚙️ Tùy Chỉnh Cluster & Hiệu Năng

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 MB | `1` | `1` |
| 1 GB | `0` (tự động) | `2` |
| 2 GB+ | `0` (tự động) | `2–4` |

```javascript
cluster: {
  workers: 0,  // 0 = tự động dùng số core CPU; đặt 1 nếu VPS yếu
  hibernation: { enabled: true, timeoutMs: 1200000 } // Ngủ sau 20 phút không hoạt động
}
```

---

<br/>

---

<br/>

# 🇫🇷 Français

> Un fichier `config.js` propre et entièrement commenté pour [NodeLink](https://github.com/PerformanC/NodeLink), sans aucune information personnelle, prêt à déployer sur n'importe quel environnement d'hébergement.

Optimisé pour la **lecture en cluster**, les **VPS à faibles ressources** et les **panels d'hébergement** (Pterodactyl, HidenCloud, etc.). Prend en charge 40+ sources audio avec tous les filtres activés.

## 📋 Table des Matières

- [Prérequis](#-prérequis)
- [Installation](#-installation-fr)
  - [Méthode A — Utiliser ce dépôt (Recommandé)](#méthode-a--utiliser-ce-dépôt-recommandé)
  - [Méthode B — NodeLink officiel + Renommer le config](#méthode-b--nodelink-officiel--renommer-le-config)
- [Configuration Obligatoire](#-configuration-obligatoire)
  - [1. Port & Mot de passe serveur](#1-port--mot-de-passe-serveur)
  - [2. YouTube Refresh Token](#2-youtube-refresh-token-fr)
  - [3. Identifiants Spotify](#3-identifiants-spotify)
- [Sources Audio Optionnelles](#-sources-audio-optionnelles)
- [Comment Lancer](#-comment-lancer)
  - [Option A — VPS / Linux standard](#option-a--vps--linux-standard)
  - [Option B — Pterodactyl / Panels web](#option-b--pterodactyl--panels-web)
- [Connecter votre Bot Discord](#-connecter-votre-bot-discord)
- [Réglages Cluster & Performance](#-réglages-cluster--performance)

---

## 🛑 Prérequis

| Requis | Détail |
|---|---|
| **Node.js** | **v22.x ou supérieur** — obligatoire |
| **npm** | Inclus avec Node.js |

```bash
node -v
# Doit afficher v22.x.x ou supérieur
```

---

## 📦 Installation {#-installation-fr}

### Méthode A — Utiliser ce dépôt (Recommandé)

```bash
# 1 — Cloner le dépôt NodeLink officiel
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# 2 — Télécharger le config.js de ce dépôt
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js

# 3 — Remplir vos identifiants (voir ci-dessous), puis :
npm install
npm run start
```

Ou cloner ce dépôt entier et copier `config.js` manuellement :

```bash
git clone https://github.com/iamprmgvyt/nodelink-full-setup.git
# Copier config.js dans votre dossier NodeLink
```

---

### Méthode B — NodeLink officiel + Renommer le config

Le dépôt NodeLink officiel fournit `config.default.js` mais pas `config.js`. Renommez-le :

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
cp config.default.js config.js
npm install
npm run start
```

> ⚠️ `config.default.js` peut manquer des champs récents. La Méthode A est recommandée pour un support complet.

---

## 🔑 Configuration Obligatoire

### 1. Port & Mot de passe serveur

En haut du fichier `config.js` :

```javascript
server: {
  host: '0.0.0.0',
  port: yourporthere,       // ← Votre port alloué (ex : 2333)
  password: 'yourpassword', // ← Un mot de passe fort
  useBunServer: false
}
```

- **VPS avec accès root :** utilisez n'importe quel port libre, ex : `2333`
- **Panel (Pterodactyl / HidenCloud) :** utilisez le port alloué par le panel, visible dans l'onglet **Réseau** de votre serveur

---

### 2. YouTube Refresh Token {#2-youtube-refresh-token-fr}

Sans ce token, YouTube bloque la lecture avec des erreurs **HTTP 429 (rate limit)** après quelques pistes.

#### Comment l'obtenir (Google Device OAuth — aucun outil tiers requis)

**Étape 1 — Demander un code d'appareil**

```bash
curl -X POST "https://oauth2.googleapis.com/device/code" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&scope=https://www.googleapis.com/auth/youtube"
```

Réponse :
```json
{
  "device_code": "AH-1Ng3f...",
  "user_code": "ABCD-EFGH",
  "verification_url": "https://www.google.com/device",
  "expires_in": 1800
}
```

**Étape 2 — Autoriser via le navigateur**

1. Ouvrez [https://www.google.com/device](https://www.google.com/device)
2. Entrez le `user_code` (ex : `ABCD-EFGH`)
3. Connectez-vous avec un **compte Google dédié** et cliquez sur **Autoriser**

**Étape 3 — Échanger contre un refresh token**

Remplacez `DEVICE_CODE_HERE` par le `device_code` de l'Étape 1 :

```bash
curl -X POST "https://oauth2.googleapis.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&client_secret=SboVhoG9s0rNafixCSGGKXAT&code=DEVICE_CODE_HERE&grant_type=http://oauth.net/grant_type/device/1.0"
```

Réponse :
```json
{
  "access_token": "ya29.xxx",
  "refresh_token": "1//0eXXXXXXXXXXXX",
  "expires_in": 3599
}
```

**Étape 4 — Coller dans `config.js`**

```javascript
clients: {
  settings: {
    TV: {
      refreshToken: ['1//0eXXXXXXXXXXXX']
      // Plusieurs tokens tournent automatiquement en cas d'échec :
      // refreshToken: ['token1', 'token2']
    }
  }
}
```

> ✅ Les refresh tokens **n'expirent pas** à moins d'être révoqués manuellement.

---

### 3. Identifiants Spotify

Nécessaires pour la recherche Spotify et le chargement de playlists/albums. L'audio est streamé via YouTube ou une source de secours.

**Comment obtenir Client ID & Client Secret :**

1. Aller sur [https://developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Se connecter → **Create App**
3. Remplir :
   - **App Name :** au choix (ex : `NodeLink`)
   - **Redirect URI :** `http://localhost`
   - Cocher **Web API**
4. Cliquer **Save** → ouvrir l'app → **Settings**
5. Copier **Client ID** et **Client Secret**

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',
  clientSecret: 'YOUR_CLIENT_SECRET_HERE',
  market: 'US',
  allowExplicit: true
}
```

> ⚠️ Ne jamais publier vos vrais identifiants sur un dépôt GitHub public.

---

## 🎧 Sources Audio Optionnelles

Toutes les sources sont déjà activées dans `config.js`. Ces identifiants sont uniquement nécessaires pour un accès premium ou une meilleure qualité.

<details>
<summary><strong>Apple Music</strong> — récupère le token automatiquement</summary>

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
Laissez `'token_here'` tel quel — NodeLink récupère le token automatiquement.
</details>

<details>
<summary><strong>Tidal</strong> — connexion automatique via Google</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
Pour le lossless, configurez [hifi-api](https://github.com/binimum/hifi-api/) et ajoutez-le à `hifiApis`.
</details>

<details>
<summary><strong>Deezer</strong> — cookie ARL pour premium/lossless</summary>

deezer.com (connecté) → F12 → Application → Cookies → copier la valeur `arl`

```javascript
deezer: { enabled: true, /* arl: 'VOTRE_ARL' */ }
```
</details>

<details>
<summary><strong>Qobuz</strong> — token utilisateur pour 320kbps / FLAC</summary>

play.qobuz.com → F12 → Application → Local Storage → `localuser` → `token`

```javascript
qobuz: { enabled: true, userToken: 'VOTRE_TOKEN', formatId: '5' }
// '5' = MP3 320kbps | '6' = FLAC | '27' = Hi-Res FLAC
```
</details>

<details>
<summary><strong>Bilibili</strong> — SESSDATA pour 4K+/premium</summary>

bilibili.com → F12 → Application → Cookies → `SESSDATA`

```javascript
bilibili: { enabled: true, sessdata: 'VOTRE_SESSDATA' }
```
</details>

<details>
<summary><strong>Audius</strong> — clé API pour plus de requêtes</summary>

[https://audius.co/settings](https://audius.co/settings) → Créer une App

```javascript
audius: { enabled: true, appName: '', apiKey: '', apiSecret: '' }
```
</details>

<details>
<summary><strong>Last.fm</strong> — paroles & métadonnées</summary>

[https://www.last.fm/api/account/create](https://www.last.fm/api/account/create)

```javascript
lastfm: { enabled: true, apiKey: 'VOTRE_CLE_API' }
```
</details>

---

## ▶️ Comment Lancer

### Option A — VPS / Linux standard

```bash
npm install && npm run start

# Fonctionnement 24/7 avec PM2
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

### Option B — Pterodactyl / Panels web

Les panels imposent une **limite de 16 caractères** pour le fichier de démarrage. `dist/src/index.js` = 18 caractères → rejeté.

**Solution :** Créez `run.js` à la racine de votre serveur :

```javascript
import './dist/src/index.js';
```

Dans le panel → onglet **Démarrage** → changer **Fichier de démarrage** en `run.js` → cliquer **Démarrer**.

> ✅ Pas besoin de SSH — le panel gère `npm install` automatiquement.

---

## 🤖 Connecter votre Bot Discord

Quand la console affiche `Successfully listening on host 0.0.0.0, port XXXX` :

```javascript
const nodes = [{
  name: 'MyNodeLink',
  host: 'your.server.domain.com', // IP/domaine VPS ou hostname du panel
  port: 2333,                      // Doit correspondre à server.port dans config.js
  password: 'your_password',       // Doit correspondre à server.password dans config.js
  secure: false                    // true UNIQUEMENT si le serveur fournit SSL sur ce port
}];
```

---

## ⚙️ Réglages Cluster & Performance

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 Mo | `1` | `1` |
| 1 Go | `0` (auto) | `2` |
| 2 Go+ | `0` (auto) | `2–4` |

```javascript
cluster: {
  workers: 0,  // 0 = automatique (1 worker par cœur CPU)
  hibernation: { enabled: true, timeoutMs: 1200000 } // Veille après 20 min d'inactivité
}
```

---

<div align="center">

**Built with ♥️ by [iamprmgvyt](https://github.com/iamprmgvyt)**

[🇬🇧 English](#-english) &nbsp;|&nbsp; [🇻🇳 Tiếng Việt](#-tiếng-việt) &nbsp;|&nbsp; [🇫🇷 Français](#-français)

</div>
