<div align="center">

# 🎵 NodeLink Full Setup

**Production-ready `config.js` for [NodeLink](https://github.com/PerformanC/NodeLink)**

<br/>

### 🌐 Choose Language / Chọn Ngôn Ngữ / Choisir la Langue / भाषा चुनें / 选择语言

<table>
<tr>
<td align="center"><a href="#english"><img src="https://flagcdn.com/48x36/gb.png" width="36"/><br/><b>English</b></a></td>
<td align="center"><a href="#tieng-viet"><img src="https://flagcdn.com/48x36/vn.png" width="36"/><br/><b>Tiếng Việt</b></a></td>
<td align="center"><a href="#francais"><img src="https://flagcdn.com/48x36/fr.png" width="36"/><br/><b>Français</b></a></td>
<td align="center"><a href="#hindi"><img src="https://flagcdn.com/48x36/in.png" width="36"/><br/><b>हिन्दी</b></a></td>
<td align="center"><a href="#zhongwen"><img src="https://flagcdn.com/48x36/cn.png" width="36"/><br/><b>中文</b></a></td>
</tr>
</table>

</div>

---

<br/>

<!-- ============================================================ -->
<!--                        ENGLISH                               -->
<!-- ============================================================ -->

<a id="english"></a>

<div align="center">

## 🇬🇧 English

</div>

> A clean, fully-commented `config.js` for [NodeLink](https://github.com/PerformanC/NodeLink), stripped of all personal credentials and ready to deploy on any hosting environment.

Optimized for **clustered playback**, **low-resource VPS**, and **panel-based hosting** (Pterodactyl, HidenCloud, etc.). Supports 40+ audio sources with all audio filters enabled.

<details>
<summary>📋 <b>Table of Contents</b></summary>

- [Prerequisites](#en-prereq)
- [Installation](#en-install)
  - [Method A — Use This Repo (Recommended)](#en-method-a)
  - [Method B — Official NodeLink + Rename Config](#en-method-b)
- [Required Configuration](#en-config)
  - [1. Server Port & Password](#en-server)
  - [2. YouTube Refresh Token](#en-yt)
  - [3. Spotify Credentials](#en-spotify)
- [Optional Sources](#en-optional)
- [How to Run](#en-run)
  - [Option A — Standard VPS / Linux](#en-run-vps)
  - [Option B — Pterodactyl / Web Panels](#en-run-panel)
- [Connecting Your Discord Bot](#en-bot)
- [Cluster & Performance Tuning](#en-cluster)
- [Troubleshooting](#en-trouble)

</details>

---

<a id="en-prereq"></a>

### 🛑 Prerequisites

| Requirement | Detail |
|---|---|
| **Node.js** | **v22.x or higher** — strictly required |
| **npm** | Bundled with Node.js |

```bash
node -v
# Must output v22.x.x or higher
```

---

<a id="en-install"></a>

### 📦 Installation

<a id="en-method-a"></a>

**Method A — Use This Repo (Recommended)**

```bash
# 1 — Clone the official NodeLink source
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# 2 — Download this repo's config.js
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js

# 3 — Fill in your credentials (see below), then:
npm install && npm run start
```

Or clone this entire repo and copy `config.js` manually:

```bash
git clone https://github.com/iamprmgvyt/nodelink-full-setup.git
# Then copy config.js into your NodeLink folder
```

<a id="en-method-b"></a>

**Method B — Official NodeLink + Rename Config**

The official NodeLink repo ships `config.default.js` — no `config.js` included.

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
cp config.default.js config.js
npm install && npm run start
```

> ⚠️ `config.default.js` may be missing newer fields. Method A is recommended.

---

<a id="en-config"></a>

### 🔑 Required Configuration

<a id="en-server"></a>

**1. Server Port & Password**

```javascript
server: {
  host: '0.0.0.0',
  port: 2333,                // ← Your port
  password: 'yourpassword',  // ← A strong password
  useBunServer: false
}
```

- **VPS:** use any open port, e.g. `2333`
- **Panel (Pterodactyl / HidenCloud):** use the port shown in your server's **Network** tab

---

<a id="en-yt"></a>

**2. YouTube Refresh Token** _(Prevents HTTP 429)_

Without this, YouTube blocks playback after a few tracks.

<details>
<summary>📖 <b>Step-by-step guide (Google Device OAuth — no third-party tools)</b></summary>

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

**Step 2 — Authorize via browser**

1. Open [https://www.google.com/device](https://www.google.com/device)
2. Enter the `user_code` (e.g. `ABCD-EFGH`)
3. Sign in with a **dedicated Google account** → click **Allow**

> 💡 Use a dedicated Google account — not your personal one.

**Step 3 — Exchange for refresh token**

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
  "expires_in": 3599
}
```

**Step 4 — Paste into `config.js`**

```javascript
clients: {
  settings: {
    TV: {
      refreshToken: ['1//0eXXXXXXXXXXXX']
      // Multiple tokens rotate automatically: ['token1', 'token2']
    }
  }
}
```

✅ Refresh tokens **do not expire** unless manually revoked — do this once only.

</details>

---

<a id="en-spotify"></a>

**3. Spotify Credentials**

NodeLink needs Spotify auth for search and playlist/album loading. Actual audio still streams via YouTube or fallback sources. You have **two options** — pick one.

<details open>
<summary>📖 <b>Option A — Use the community token provider (no Spotify account needed)</b></summary>

`config.js` already ships with this pre-filled:

```javascript
spotify: {
  enabled: true,
  clientId: '',
  clientSecret: '',
  externalAuthUrl: 'http://get.1lucas1apk.fun/spotify/gettoken',
  market: 'US'
}
```

Leave `clientId` / `clientSecret` empty and keep `externalAuthUrl` as-is. NodeLink calls this URL **server-to-server** and parses the returned JSON token automatically — **do nothing further**.

> ⚠️ **If you open this URL directly in a browser, you'll see a Cloudflare error (1033 / "Tunnel error").** This is expected and does **not** mean it's broken for NodeLink. See [Troubleshooting](#en-trouble) below for why.
>
> This is a free community service — it may occasionally go down. If NodeLink logs show repeated Spotify failures, switch to Option B below.

</details>

<details>
<summary>📖 <b>Option B — Use your own Spotify Developer credentials (more reliable)</b></summary>

1. Go to [https://developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Log in → **Create App**
3. Fill in: App Name (anything), Redirect URI: `http://localhost`, check **Web API** → **Save**
4. Open the app → **Settings** → copy **Client ID** and **Client Secret**

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',
  clientSecret: 'YOUR_CLIENT_SECRET_HERE',
  externalAuthUrl: '',   // ← clear this so NodeLink uses your own credentials instead
  market: 'US'
}
```

⚠️ Never commit real credentials to a public GitHub repository.

</details>

---

<a id="en-optional"></a>

### 🎧 Optional Sources

<details>
<summary><b>Apple Music</b> — auto-fetches token by default</summary>

Leave `'token_here'` as-is. NodeLink fetches the token automatically.

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
</details>

<details>
<summary><b>Tidal</b> — auto-fetches via Google login</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
For lossless, see [hifi-api](https://github.com/binimum/hifi-api/).
</details>

<details>
<summary><b>Deezer</b> — ARL cookie for premium/lossless</summary>

deezer.com (logged in) → F12 → Application → Cookies → copy `arl`

```javascript
deezer: { enabled: true /*, arl: 'YOUR_ARL' */ }
```
</details>

<details>
<summary><b>Qobuz</b> — user token for 320kbps / FLAC</summary>

play.qobuz.com → F12 → Application → LocalStorage → `localuser` → `token`

```javascript
qobuz: { enabled: true, userToken: 'YOUR_TOKEN', formatId: '5' }
// '5'=MP3 320kbps | '6'=FLAC | '27'=Hi-Res FLAC
```
</details>

<details>
<summary><b>VK Music</b></summary>

vk.com → F12 → Network → POST `/?act=web_token`
— `userToken`: Response → `access_token`
— `userCookie`: Request Headers → `cookie`

```javascript
vkmusic: { enabled: true, userToken: '', userCookie: '' }
```
</details>

<details>
<summary><b>Bilibili</b> — SESSDATA for 4K+/premium</summary>

bilibili.com → F12 → Application → Cookies → `SESSDATA`

```javascript
bilibili: { enabled: true, sessdata: 'YOUR_SESSDATA' }
```
</details>

<details>
<summary><b>Audius</b> — API key for higher rate limits</summary>

[audius.co/settings](https://audius.co/settings) → Create an App

```javascript
audius: { enabled: true, appName: '', apiKey: '', apiSecret: '' }
```
</details>

<details>
<summary><b>Pandora</b> — community token provider (same pattern as Spotify)</summary>

```javascript
pandora: {
  enabled: true,
  remoteTokenUrl: 'http://get.1lucas1apk.fun/pandora/gettoken'
}
```
Same 1033 browser error applies — it's a server-to-server API, not a webpage.
</details>

<details>
<summary><b>Last.fm</b> — lyrics & metadata</summary>

[last.fm/api/account/create](https://www.last.fm/api/account/create)

```javascript
lastfm: { enabled: true, apiKey: 'YOUR_API_KEY' }
```
</details>

---

<a id="en-run"></a>

### ▶️ How to Run

<a id="en-run-vps"></a>

**Option A — Standard VPS / Linux**

```bash
npm install && npm run start

# 24/7 with PM2
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

<a id="en-run-panel"></a>

**Option B — Pterodactyl / Web Panels**

Panels enforce a **16-character limit** on startup file paths. `dist/src/index.js` = 18 chars → rejected.

Create `run.js` in your server root:

```javascript
import './dist/src/index.js';
```

Panel → **Startup** tab → set **Startup File** to `run.js` → click **Start**.
> ✅ No SSH needed — the panel handles `npm install` automatically.

---

<a id="en-bot"></a>

### 🤖 Connecting Your Discord Bot

When the console shows `Successfully listening on host 0.0.0.0, port XXXX`:

```javascript
const nodes = [{
  name: 'MyNodeLink',
  host: 'your.server.domain.com', // VPS IP or panel hostname
  port: 2333,                      // Must match config.js → server.port
  password: 'your_password',       // Must match config.js → server.password
  secure: false                    // true ONLY if host provides SSL on this port
}];
```

---

<a id="en-cluster"></a>

### ⚙️ Cluster & Performance Tuning

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 MB | `1` | `1` |
| 1 GB | `0` (auto) | `2` |
| 2 GB+ | `0` (auto) | `2–4` |

```javascript
cluster: {
  workers: 0,  // 0 = 1 per CPU core; set to 1 on tiny VPS
  hibernation: { enabled: true, timeoutMs: 1200000 } // Sleep after 20 min idle
}
```

---

<a id="en-trouble"></a>

### 🛠️ Troubleshooting

**"Cloudflare Tunnel error 1033" when opening a `externalAuthUrl` / `remoteTokenUrl` in a browser**

This is **expected and not a bug** when it happens in a browser. Two separate reasons combine here:

1. **These URLs are API endpoints, not webpages.** `get.1lucas1apk.fun/spotify/gettoken` and `/pandora/gettoken` are meant to be called by NodeLink itself (server-to-server), returning raw JSON like `{"access_token": "...", "expires_in": 3600}`. There's no HTML page to render, so visiting it directly looks broken even when it works fine for NodeLink.
2. **Error 1033 specifically means Cloudflare's tunnel daemon (`cloudflared`) on the provider's server is currently offline or unreachable.** This is a temporary outage on the token-provider's end, unrelated to your NodeLink setup. If NodeLink itself starts failing to fetch Spotify/Pandora tokens (visible in `[ERROR]` logs), the service is genuinely down — switch to **Option B (your own Spotify credentials)** until it's back.

<div align="right"><a href="#english">⬆ Back to top</a></div>

---

<br/>

<!-- ============================================================ -->
<!--                       TIẾNG VIỆT                             -->
<!-- ============================================================ -->

<a id="tieng-viet"></a>

<div align="center">

## 🇻🇳 Tiếng Việt

</div>

> File `config.js` sạch, đầy đủ comment cho [NodeLink](https://github.com/PerformanC/NodeLink), đã xóa toàn bộ credential cá nhân, sẵn sàng deploy trên mọi môi trường hosting.

Tối ưu cho **phát nhạc đa luồng**, **VPS ít RAM**, và **panel hosting** (Pterodactyl, HidenCloud,...). Hỗ trợ 40+ nguồn âm thanh với đầy đủ bộ lọc.

<details>
<summary>📋 <b>Mục Lục</b></summary>

- [Yêu Cầu](#vn-prereq)
- [Cài Đặt](#vn-install)
  - [Cách A — Dùng Repo Này (Khuyến Nghị)](#vn-method-a)
  - [Cách B — NodeLink Gốc + Đổi Tên Config](#vn-method-b)
- [Cấu Hình Bắt Buộc](#vn-config)
  - [1. Port & Mật Khẩu Server](#vn-server)
  - [2. YouTube Refresh Token](#vn-yt)
  - [3. Spotify Credentials](#vn-spotify)
- [Nguồn Nhạc Tùy Chọn](#vn-optional)
- [Cách Chạy](#vn-run)
  - [Cách A — VPS / Linux thông thường](#vn-run-vps)
  - [Cách B — Pterodactyl / Web Panel](#vn-run-panel)
- [Kết Nối Discord Bot](#vn-bot)
- [Tùy Chỉnh Cluster & Hiệu Năng](#vn-cluster)
- [Xử Lý Lỗi](#vn-trouble)

</details>

---

<a id="vn-prereq"></a>

### 🛑 Yêu Cầu

| Yêu cầu | Chi tiết |
|---|---|
| **Node.js** | **v22.x trở lên** — bắt buộc |
| **npm** | Đi kèm với Node.js |

```bash
node -v
# Phải ra v22.x.x trở lên
```

---

<a id="vn-install"></a>

### 📦 Cài Đặt

<a id="vn-method-a"></a>

**Cách A — Dùng Repo Này (Khuyến Nghị)**

```bash
# 1 — Clone NodeLink gốc từ PerformanC
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# 2 — Tải config.js từ repo này về
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js

# 3 — Điền thông tin của bạn (xem bên dưới), rồi:
npm install && npm run start
```

Hoặc clone cả repo này rồi copy `config.js` thủ công:

```bash
git clone https://github.com/iamprmgvyt/nodelink-full-setup.git
# Copy config.js vào thư mục NodeLink
```

<a id="vn-method-b"></a>

**Cách B — NodeLink Gốc + Đổi Tên Config**

NodeLink gốc chỉ có `config.default.js`. Đổi tên và dùng luôn:

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
cp config.default.js config.js
npm install && npm run start
```

> ⚠️ `config.default.js` có thể thiếu field mới. Nên dùng Cách A.

---

<a id="vn-config"></a>

### 🔑 Cấu Hình Bắt Buộc

<a id="vn-server"></a>

**1. Port & Mật Khẩu Server**

```javascript
server: {
  host: '0.0.0.0',
  port: 2333,                // ← Điền port của bạn
  password: 'yourpassword',  // ← Đặt mật khẩu mạnh
  useBunServer: false
}
```

- **VPS tự quản lý:** dùng bất kỳ port nào còn trống, vd `2333`
- **Panel (Pterodactyl / HidenCloud):** dùng port được panel cấp, xem tab **Network** của server

---

<a id="vn-yt"></a>

**2. YouTube Refresh Token** _(Tránh lỗi HTTP 429)_

Không có token này, YouTube sẽ chặn phát nhạc với lỗi rate limit sau vài bài.

<details>
<summary>📖 <b>Hướng dẫn lấy token (Google Device OAuth — không cần tool bên thứ 3)</b></summary>

**Bước 1 — Lấy device code**

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

**Bước 2 — Xác thực trên trình duyệt**

1. Mở [https://www.google.com/device](https://www.google.com/device)
2. Nhập `user_code` (vd: `ABCD-EFGH`)
3. Đăng nhập bằng **tài khoản Google riêng** → nhấn **Allow**

> 💡 Nên dùng tài khoản Google riêng, không dùng tài khoản cá nhân.

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
      // Nhiều token xoay vòng tự động: ['token1', 'token2']
    }
  }
}
```

✅ Refresh token **không hết hạn** trừ khi bạn tự thu hồi — chỉ làm một lần duy nhất.

</details>

---

<a id="vn-spotify"></a>

**3. Spotify Credentials**

NodeLink cần Spotify auth để tìm kiếm và load playlist/album. Âm thanh thực tế vẫn stream qua YouTube hoặc nguồn fallback. Bạn có **2 cách** — chọn 1 trong 2.

<details open>
<summary>📖 <b>Cách A — Dùng community token provider (không cần tài khoản Spotify)</b></summary>

`config.js` đã có sẵn cấu hình này:

```javascript
spotify: {
  enabled: true,
  clientId: '',
  clientSecret: '',
  externalAuthUrl: 'http://get.1lucas1apk.fun/spotify/gettoken',
  market: 'US'
}
```

Để trống `clientId` / `clientSecret`, giữ nguyên `externalAuthUrl`. NodeLink sẽ tự gọi URL này **từ server sang server** và parse token JSON trả về — **không cần làm gì thêm**.

> ⚠️ **Nếu bạn mở URL này trực tiếp bằng trình duyệt, bạn sẽ thấy lỗi Cloudflare (1033 / "Tunnel error").** Đây là điều bình thường và **không** có nghĩa là nó hỏng đối với NodeLink. Xem mục [Xử Lý Lỗi](#vn-trouble) bên dưới để hiểu rõ.
>
> Đây là dịch vụ cộng đồng miễn phí — đôi khi có thể bị down. Nếu log NodeLink báo lỗi liên tục khi lấy Spotify token, hãy chuyển sang Cách B bên dưới.

</details>

<details>
<summary>📖 <b>Cách B — Dùng Spotify Developer credentials của riêng bạn (ổn định hơn)</b></summary>

1. Vào [https://developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Đăng nhập → **Create App**
3. Điền: App Name (tùy ý), Redirect URI: `http://localhost`, tích **Web API** → **Save**
4. Mở app vừa tạo → **Settings** → copy **Client ID** và **Client Secret**

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',
  clientSecret: 'YOUR_CLIENT_SECRET_HERE',
  externalAuthUrl: '',   // ← xóa trống để NodeLink dùng credential riêng của bạn
  market: 'US'
}
```

⚠️ Không commit credential thật lên GitHub public.

</details>

---

<a id="vn-optional"></a>

### 🎧 Nguồn Nhạc Tùy Chọn

<details>
<summary><b>Apple Music</b> — tự lấy token, không cần làm gì</summary>

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
Để nguyên `'token_here'` — NodeLink tự fetch.
</details>

<details>
<summary><b>Tidal</b> — tự đăng nhập qua Google</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
Muốn lossless: cài [hifi-api](https://github.com/binimum/hifi-api/) rồi thêm vào `hifiApis`.
</details>

<details>
<summary><b>Deezer</b> — cần ARL cookie cho premium/lossless</summary>

deezer.com (đã đăng nhập) → F12 → Application → Cookies → copy `arl`

```javascript
deezer: { enabled: true /*, arl: 'YOUR_ARL' */ }
```
</details>

<details>
<summary><b>Qobuz</b> — cần token cho 320kbps / FLAC</summary>

play.qobuz.com → F12 → Application → Local Storage → `localuser` → `token`

```javascript
qobuz: { enabled: true, userToken: 'YOUR_TOKEN', formatId: '5' }
```
</details>

<details>
<summary><b>VK Music</b></summary>

vk.com → F12 → Network → POST `/?act=web_token`
— `userToken`: Response → `access_token`
— `userCookie`: Request Headers → `cookie` (copy toàn bộ)

```javascript
vkmusic: { enabled: true, userToken: '', userCookie: '' }
```
</details>

<details>
<summary><b>Bilibili</b> — cần SESSDATA cho 4K+/premium</summary>

bilibili.com → F12 → Application → Cookies → `SESSDATA`

```javascript
bilibili: { enabled: true, sessdata: 'YOUR_SESSDATA' }
```
</details>

<details>
<summary><b>Audius</b> — API key tăng rate limit</summary>

[audius.co/settings](https://audius.co/settings) → Create an App

```javascript
audius: { enabled: true, appName: '', apiKey: '', apiSecret: '' }
```
</details>

<details>
<summary><b>Pandora</b> — community token provider (giống Spotify)</summary>

```javascript
pandora: {
  enabled: true,
  remoteTokenUrl: 'http://get.1lucas1apk.fun/pandora/gettoken'
}
```
Cũng gặp lỗi 1033 khi mở bằng browser — vì đây là API server-to-server, không phải trang web.
</details>

<details>
<summary><b>Last.fm</b> — lyrics & metadata</summary>

[last.fm/api/account/create](https://www.last.fm/api/account/create)

```javascript
lastfm: { enabled: true, apiKey: 'YOUR_API_KEY' }
```
</details>

---

<a id="vn-run"></a>

### ▶️ Cách Chạy

<a id="vn-run-vps"></a>

**Cách A — VPS / Linux thông thường**

```bash
npm install && npm run start

# Chạy nền 24/7 với PM2
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

<a id="vn-run-panel"></a>

**Cách B — Pterodactyl / Web Panel**

Panel giới hạn **16 ký tự** cho đường dẫn file khởi động. `dist/src/index.js` = 18 ký tự → bị từ chối.

Tạo file `run.js` trong thư mục gốc server:

```javascript
import './dist/src/index.js';
```

Panel → tab **Startup** → đổi **Startup File** thành `run.js` → nhấn **Start**.
> ✅ Không cần SSH — panel tự chạy `npm install`.

---

<a id="vn-bot"></a>

### 🤖 Kết Nối Discord Bot

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

<a id="vn-cluster"></a>

### ⚙️ Tùy Chỉnh Cluster & Hiệu Năng

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 MB | `1` | `1` |
| 1 GB | `0` (tự động) | `2` |
| 2 GB+ | `0` (tự động) | `2–4` |

```javascript
cluster: {
  workers: 0,  // 0 = tự dùng số core CPU; đặt 1 nếu VPS yếu
  hibernation: { enabled: true, timeoutMs: 1200000 } // Ngủ sau 20 phút không hoạt động
}
```

---

<a id="vn-trouble"></a>

### 🛠️ Xử Lý Lỗi

**Lỗi "Cloudflare Tunnel error 1033" khi mở `externalAuthUrl` / `remoteTokenUrl` bằng trình duyệt**

Đây là điều **bình thường, không phải bug** khi xảy ra trên trình duyệt. Có 2 lý do cộng lại:

1. **Các URL này là API endpoint, không phải trang web.** `get.1lucas1apk.fun/spotify/gettoken` và `/pandora/gettoken` được thiết kế để NodeLink gọi **server-to-server**, trả về JSON thô như `{"access_token": "...", "expires_in": 3600}`. Không có giao diện HTML để hiển thị, nên mở trực tiếp trông như bị lỗi dù vẫn hoạt động bình thường với NodeLink.
2. **Lỗi 1033 cụ thể có nghĩa là Cloudflare Tunnel daemon (`cloudflared`) trên server của nhà cung cấp đang offline hoặc không kết nối được.** Đây là sự cố tạm thời từ phía họ, không liên quan đến setup của bạn. Nếu chính NodeLink cũng bắt đầu báo lỗi khi lấy token Spotify/Pandora (xem trong log `[ERROR]`), thì dịch vụ thật sự đang down — hãy chuyển sang **Cách B (dùng credential Spotify riêng)** cho đến khi nó hoạt động trở lại.

<div align="right"><a href="#tieng-viet">⬆ Lên đầu mục</a></div>

---

<br/>

<!-- ============================================================ -->
<!--                        FRANÇAIS                              -->
<!-- ============================================================ -->

<a id="francais"></a>

<div align="center">

## 🇫🇷 Français

</div>

> Un fichier `config.js` propre et entièrement commenté pour [NodeLink](https://github.com/PerformanC/NodeLink), sans aucune information personnelle, prêt à déployer sur n'importe quel environnement d'hébergement.

Optimisé pour la **lecture en cluster**, les **VPS à faibles ressources** et les **panels d'hébergement** (Pterodactyl, HidenCloud, etc.). Prend en charge 40+ sources audio avec tous les filtres activés.

<details>
<summary>📋 <b>Table des Matières</b></summary>

- [Prérequis](#fr-prereq)
- [Installation](#fr-install)
  - [Méthode A — Utiliser ce dépôt (Recommandé)](#fr-method-a)
  - [Méthode B — NodeLink officiel + Renommer le config](#fr-method-b)
- [Configuration Obligatoire](#fr-config)
  - [1. Port & Mot de passe serveur](#fr-server)
  - [2. YouTube Refresh Token](#fr-yt)
  - [3. Identifiants Spotify](#fr-spotify)
- [Sources Audio Optionnelles](#fr-optional)
- [Comment Lancer](#fr-run)
  - [Option A — VPS / Linux standard](#fr-run-vps)
  - [Option B — Pterodactyl / Panels web](#fr-run-panel)
- [Connecter votre Bot Discord](#fr-bot)
- [Réglages Cluster & Performance](#fr-cluster)
- [Dépannage](#fr-trouble)

</details>

---

<a id="fr-prereq"></a>

### 🛑 Prérequis

| Requis | Détail |
|---|---|
| **Node.js** | **v22.x ou supérieur** — obligatoire |
| **npm** | Inclus avec Node.js |

```bash
node -v
# Doit afficher v22.x.x ou supérieur
```

---

<a id="fr-install"></a>

### 📦 Installation

<a id="fr-method-a"></a>

**Méthode A — Utiliser ce dépôt (Recommandé)**

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js
npm install && npm run start
```

<a id="fr-method-b"></a>

**Méthode B — NodeLink officiel + Renommer le config**

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
cp config.default.js config.js
npm install && npm run start
```

> ⚠️ `config.default.js` peut manquer des champs récents. La Méthode A est recommandée.

---

<a id="fr-config"></a>

### 🔑 Configuration Obligatoire

<a id="fr-server"></a>

**1. Port & Mot de passe serveur**

```javascript
server: {
  host: '0.0.0.0',
  port: 2333,                // ← Votre port alloué
  password: 'yourpassword',  // ← Un mot de passe fort
  useBunServer: false
}
```

<a id="fr-yt"></a>

**2. YouTube Refresh Token** _(Prévient les erreurs HTTP 429)_

<details>
<summary>📖 <b>Guide étape par étape (Google Device OAuth)</b></summary>

**Étape 1 — Demander un code d'appareil**

```bash
curl -X POST "https://oauth2.googleapis.com/device/code" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&scope=https://www.googleapis.com/auth/youtube"
```

**Étape 2 — Autoriser via le navigateur**

1. Ouvrir [https://www.google.com/device](https://www.google.com/device)
2. Entrer le `user_code` reçu dans la réponse
3. Se connecter avec un **compte Google dédié** → **Autoriser**

**Étape 3 — Échanger contre un refresh token**

```bash
curl -X POST "https://oauth2.googleapis.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&client_secret=SboVhoG9s0rNafixCSGGKXAT&code=DEVICE_CODE_HERE&grant_type=http://oauth.net/grant_type/device/1.0"
```

**Étape 4 — Coller dans `config.js`**

```javascript
clients: { settings: { TV: { refreshToken: ['1//0eXXXXXXXXXXXX'] } } }
```

✅ Les refresh tokens **n'expirent pas** à moins d'être révoqués manuellement.

</details>

---

<a id="fr-spotify"></a>

**3. Identifiants Spotify**

Deux options possibles — choisissez-en une.

<details open>
<summary>📖 <b>Option A — Fournisseur de token communautaire (sans compte Spotify)</b></summary>

```javascript
spotify: {
  enabled: true,
  clientId: '',
  clientSecret: '',
  externalAuthUrl: 'http://get.1lucas1apk.fun/spotify/gettoken',
  market: 'US'
}
```

Laissez `clientId`/`clientSecret` vides. NodeLink appelle cette URL **serveur-à-serveur** automatiquement.

> ⚠️ **Ouvrir cette URL dans un navigateur affichera une erreur Cloudflare (1033).** C'est normal — voir [Dépannage](#fr-trouble) ci-dessous.

</details>

<details>
<summary>📖 <b>Option B — Vos propres identifiants Spotify (plus fiable)</b></summary>

1. [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard) → **Create App**
2. App Name (au choix), Redirect URI: `http://localhost`, cocher **Web API** → **Save**
3. Ouvrir l'app → **Settings** → copier **Client ID** et **Client Secret**

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',
  clientSecret: 'YOUR_CLIENT_SECRET_HERE',
  externalAuthUrl: '',
  market: 'US'
}
```

</details>

---

<a id="fr-optional"></a>

### 🎧 Sources Audio Optionnelles

<details><summary><b>Apple Music</b> — token automatique</summary>

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
</details>

<details><summary><b>Tidal</b> — connexion automatique via Google</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
</details>

<details><summary><b>Deezer</b> — cookie ARL pour premium/lossless</summary>

deezer.com → F12 → Application → Cookies → copier `arl`

```javascript
deezer: { enabled: true /*, arl: 'VOTRE_ARL' */ }
```
</details>

<details><summary><b>Qobuz</b> — token pour 320kbps / FLAC</summary>

```javascript
qobuz: { enabled: true, userToken: 'VOTRE_TOKEN', formatId: '5' }
```
</details>

<details><summary><b>Bilibili</b> — SESSDATA pour 4K+/premium</summary>

```javascript
bilibili: { enabled: true, sessdata: 'VOTRE_SESSDATA' }
```
</details>

<details><summary><b>Pandora</b> — fournisseur de token communautaire</summary>

```javascript
pandora: { enabled: true, remoteTokenUrl: 'http://get.1lucas1apk.fun/pandora/gettoken' }
```
Même erreur 1033 dans un navigateur — c'est une API serveur-à-serveur.
</details>

---

<a id="fr-run"></a>

### ▶️ Comment Lancer

<a id="fr-run-vps"></a>

**Option A — VPS / Linux standard**

```bash
npm install && npm run start
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

<a id="fr-run-panel"></a>

**Option B — Pterodactyl / Panels web**

```javascript
import './dist/src/index.js';
```

Panel → onglet **Démarrage** → **Fichier de démarrage** : `run.js` → **Démarrer**.

---

<a id="fr-bot"></a>

### 🤖 Connecter votre Bot Discord

```javascript
const nodes = [{
  name: 'MyNodeLink',
  host: 'your.server.domain.com',
  port: 2333,
  password: 'your_password',
  secure: false
}];
```

---

<a id="fr-cluster"></a>

### ⚙️ Réglages Cluster & Performance

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 Mo | `1` | `1` |
| 1 Go | `0` (auto) | `2` |
| 2 Go+ | `0` (auto) | `2–4` |

---

<a id="fr-trouble"></a>

### 🛠️ Dépannage

**Erreur "Cloudflare Tunnel error 1033" en ouvrant `externalAuthUrl` / `remoteTokenUrl` dans un navigateur**

C'est **normal**, pas un bug, deux raisons combinées :

1. **Ce sont des endpoints API, pas des pages web.** Ils renvoient du JSON brut destiné à NodeLink, pas une page HTML — donc les visiter directement semble "cassé" même quand tout fonctionne pour NodeLink.
2. **L'erreur 1033 signifie que le tunnel Cloudflare (`cloudflared`) du fournisseur est hors ligne.** C'est une panne temporaire chez eux. Si NodeLink lui-même échoue à récupérer les tokens (visible dans les logs `[ERROR]`), passez à l'**Option B** en attendant.

<div align="right"><a href="#francais">⬆ Retour en haut</a></div>

---

<br/>

<!-- ============================================================ -->
<!--                         HINDI                                -->
<!-- ============================================================ -->

<a id="hindi"></a>

<div align="center">

## 🇮🇳 हिन्दी

</div>

> [NodeLink](https://github.com/PerformanC/NodeLink) के लिए एक साफ़, पूरी तरह से टिप्पणी किया गया `config.js` फ़ाइल — सभी व्यक्तिगत credentials हटाए गए, किसी भी hosting पर deploy के लिए तैयार।

**क्लस्टर प्लेबैक**, **कम RAM VPS**, और **पैनल होस्टिंग** के लिए अनुकूलित। 40+ ऑडियो स्रोत समर्थित।

<details>
<summary>📋 <b>विषय सूची</b></summary>

- [पूर्व-आवश्यकताएं](#hi-prereq)
- [इंस्टॉलेशन](#hi-install)
- [आवश्यक कॉन्फ़िगरेशन](#hi-config)
  - [1. Server Port और Password](#hi-server)
  - [2. YouTube Refresh Token](#hi-yt)
  - [3. Spotify Credentials](#hi-spotify)
- [वैकल्पिक स्रोत](#hi-optional)
- [कैसे चलाएं](#hi-run)
- [Discord Bot कनेक्ट करें](#hi-bot)
- [Cluster और Performance](#hi-cluster)
- [समस्या निवारण](#hi-trouble)

</details>

---

<a id="hi-prereq"></a>

### 🛑 पूर्व-आवश्यकताएं

| आवश्यकता | विवरण |
|---|---|
| **Node.js** | **v22.x या उससे ऊपर** — अनिवार्य |
| **npm** | Node.js के साथ आता है |

```bash
node -v
```

---

<a id="hi-install"></a>

### 📦 इंस्टॉलेशन

```bash
# Official NodeLink clone करें
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink

# इस रिपो का config.js डाउनलोड करें
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js

npm install && npm run start
```

वैकल्पिक तरीका — official `config.default.js` का नाम बदलें:

```bash
cp config.default.js config.js
```

> ⚠️ `config.default.js` में नए fields नहीं हो सकते। ऊपर वाला तरीका बेहतर है।

---

<a id="hi-config"></a>

### 🔑 आवश्यक कॉन्फ़िगरेशन

<a id="hi-server"></a>

**1. Server Port और Password**

```javascript
server: {
  host: '0.0.0.0',
  port: 2333,                // ← अपना port डालें
  password: 'yourpassword',  // ← एक मजबूत password
  useBunServer: false
}
```

---

<a id="hi-yt"></a>

**2. YouTube Refresh Token** _(HTTP 429 error से बचाता है)_

<details>
<summary>📖 <b>Token कैसे प्राप्त करें (Google Device OAuth)</b></summary>

**चरण 1 — Device code मांगें**

```bash
curl -X POST "https://oauth2.googleapis.com/device/code" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&scope=https://www.googleapis.com/auth/youtube"
```

**चरण 2 — Browser में authorize करें**

1. [https://www.google.com/device](https://www.google.com/device) खोलें
2. `user_code` डालें
3. **अलग Google account** से login करें → **Allow** दबाएं

**चरण 3 — Token प्राप्त करें**

```bash
curl -X POST "https://oauth2.googleapis.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&client_secret=SboVhoG9s0rNafixCSGGKXAT&code=DEVICE_CODE_HERE&grant_type=http://oauth.net/grant_type/device/1.0"
```

**चरण 4 — `config.js` में डालें**

```javascript
clients: { settings: { TV: { refreshToken: ['1//0eXXXXXXXXXXXX'] } } }
```

✅ Refresh token **expire नहीं होता** जब तक आप इसे revoke न करें।

</details>

---

<a id="hi-spotify"></a>

**3. Spotify Credentials**

2 तरीके उपलब्ध हैं — एक चुनें।

<details open>
<summary>📖 <b>तरीका A — Community token provider (Spotify account की जरूरत नहीं)</b></summary>

```javascript
spotify: {
  enabled: true,
  clientId: '',
  clientSecret: '',
  externalAuthUrl: 'http://get.1lucas1apk.fun/spotify/gettoken',
  market: 'US'
}
```

`clientId`/`clientSecret` खाली रखें। NodeLink यह URL **server-to-server** अपने आप call करेगा।

> ⚠️ **इस URL को browser में खोलने पर Cloudflare error (1033) दिखेगा।** यह सामान्य है — नीचे [समस्या निवारण](#hi-trouble) देखें।

</details>

<details>
<summary>📖 <b>तरीका B — अपने खुद के Spotify credentials (ज्यादा भरोसेमंद)</b></summary>

1. [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard) → login करें
2. **Create App** → App Name (कुछ भी), Redirect URI: `http://localhost`, **Web API** check करें → **Save**
3. App खोलें → **Settings** → **Client ID** और **Client Secret** copy करें

```javascript
spotify: {
  enabled: true,
  clientId: 'YOUR_CLIENT_ID_HERE',
  clientSecret: 'YOUR_CLIENT_SECRET_HERE',
  externalAuthUrl: '',
  market: 'US'
}
```

</details>

---

<a id="hi-optional"></a>

### 🎧 वैकल्पिक स्रोत

<details><summary><b>Apple Music</b> — token स्वचालित रूप से प्राप्त होता है</summary>

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
</details>

<details><summary><b>Tidal</b> — Google login से स्वचालित</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
</details>

<details><summary><b>Deezer</b> — premium के लिए ARL cookie</summary>

```javascript
deezer: { enabled: true /*, arl: 'YOUR_ARL' */ }
```
</details>

<details><summary><b>Bilibili</b> — 4K+ के लिए SESSDATA</summary>

```javascript
bilibili: { enabled: true, sessdata: 'YOUR_SESSDATA' }
```
</details>

<details><summary><b>Pandora</b> — community token provider</summary>

```javascript
pandora: { enabled: true, remoteTokenUrl: 'http://get.1lucas1apk.fun/pandora/gettoken' }
```
यह भी browser में 1033 error देगा — क्योंकि यह server-to-server API है।
</details>

---

<a id="hi-run"></a>

### ▶️ कैसे चलाएं

```bash
npm install && npm run start

# PM2 से 24/7 चलाएं
npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

**Pterodactyl / Web Panel:**

```javascript
import './dist/src/index.js';
```

Panel → **Startup** → **Startup File** को `run.js` करें → **Start** दबाएं।

---

<a id="hi-bot"></a>

### 🤖 Discord Bot कनेक्ट करें

```javascript
const nodes = [{
  name: 'MyNodeLink',
  host: 'your.server.domain.com',
  port: 2333,
  password: 'your_password',
  secure: false
}];
```

---

<a id="hi-cluster"></a>

### ⚙️ Cluster और Performance

| RAM | `workers` | `microWorkers` |
|---|---|---|
| 512 MB | `1` | `1` |
| 1 GB | `0` (auto) | `2` |
| 2 GB+ | `0` (auto) | `2–4` |

---

<a id="hi-trouble"></a>

### 🛠️ समस्या निवारण

**Browser में `externalAuthUrl` / `remoteTokenUrl` खोलने पर "Cloudflare Tunnel error 1033"**

यह **सामान्य है, bug नहीं** — दो कारण मिलकर:

1. **ये URLs API endpoint हैं, webpage नहीं।** ये NodeLink के लिए raw JSON return करते हैं, HTML page नहीं — इसलिए browser में खोलने पर "टूटा हुआ" लगता है जबकि NodeLink के लिए सही काम करता है।
2. **Error 1033 का मतलब है कि provider के server पर Cloudflare Tunnel (`cloudflared`) offline है।** यह उनकी तरफ से अस्थायी समस्या है। अगर NodeLink खुद भी token लाने में fail हो (logs में `[ERROR]` दिखे), तो सर्विस सच में down है — तब तक **तरीका B** इस्तेमाल करें।

<div align="right"><a href="#hindi">⬆ ऊपर जाएं</a></div>

---

<br/>

<!-- ============================================================ -->
<!--                         中文                                  -->
<!-- ============================================================ -->

<a id="zhongwen"></a>

<div align="center">

## 🇨🇳 中文

</div>

> 一份干净、注释完整的 [NodeLink](https://github.com/PerformanC/NodeLink) `config.js` 配置文件，已移除所有个人凭证，可在任何托管环境直接部署。

针对**集群播放**、**低配 VPS** 和**面板托管**优化。支持 40+ 音频源。

<details>
<summary>📋 <b>目录</b></summary>

- [前置条件](#zh-prereq)
- [安装](#zh-install)
- [必要配置](#zh-config)
  - [1. 服务器端口和密码](#zh-server)
  - [2. YouTube Refresh Token](#zh-yt)
  - [3. Spotify 凭证](#zh-spotify)
- [可选音频源](#zh-optional)
- [如何运行](#zh-run)
- [连接 Discord 机器人](#zh-bot)
- [集群与性能调优](#zh-cluster)
- [常见问题排查](#zh-trouble)

</details>

---

<a id="zh-prereq"></a>

### 🛑 前置条件

| 要求 | 详情 |
|---|---|
| **Node.js** | **v22.x 或更高版本** — 强制要求 |
| **npm** | 随 Node.js 一并安装 |

```bash
node -v
```

---

<a id="zh-install"></a>

### 📦 安装

```bash
git clone https://github.com/PerformanC/NodeLink.git
cd NodeLink
curl -o config.js https://raw.githubusercontent.com/iamprmgvyt/nodelink-full-setup/main/config.js
npm install && npm run start
```

或重命名官方 `config.default.js`：

```bash
cp config.default.js config.js
```

> ⚠️ `config.default.js` 可能缺少新字段，推荐使用上面的方法。

---

<a id="zh-config"></a>

### 🔑 必要配置

<a id="zh-server"></a>

**1. 服务器端口和密码**

```javascript
server: {
  host: '0.0.0.0',
  port: 2333,                // ← 填入你的端口
  password: 'yourpassword',  // ← 设置强密码
  useBunServer: false
}
```

---

<a id="zh-yt"></a>

**2. YouTube Refresh Token** _(防止 HTTP 429 错误)_

<details>
<summary>📖 <b>获取方法（Google Device OAuth）</b></summary>

**第一步 — 请求设备码**

```bash
curl -X POST "https://oauth2.googleapis.com/device/code" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&scope=https://www.googleapis.com/auth/youtube"
```

**第二步 — 在浏览器授权**

1. 打开 [https://www.google.com/device](https://www.google.com/device)
2. 输入 `user_code`
3. 用**专用 Google 账号**登录 → 点击**允许**

**第三步 — 获取 refresh token**

```bash
curl -X POST "https://oauth2.googleapis.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=861556708454-d6dlm3lh05idd8npek18k6be8ba3oc68.apps.googleusercontent.com&client_secret=SboVhoG9s0rNafixCSGGKXAT&code=DEVICE_CODE_HERE&grant_type=http://oauth.net/grant_type/device/1.0"
```

**第四步 — 粘贴到 `config.js`**

```javascript
clients: { settings: { TV: { refreshToken: ['1//0eXXXXXXXXXXXX'] } } }
```

✅ Refresh token **不会过期**，除非手动撤销。

</details>

---

<a id="zh-spotify"></a>

**3. Spotify 凭证**

有 **两种方式** — 任选其一。

<details open>
<summary>📖 <b>方式 A — 使用社区 token 提供商（无需 Spotify 账号）</b></summary>

```javascript
spotify: {
  enabled: true,
  clientId: '',
  clientSecret: '',
  externalAuthUrl: 'http://get.1lucas1apk.fun/spotify/gettoken',
  market: 'US'
}
```

保持 `clientId`/`clientSecret` 为空。NodeLink 会自动以**服务器对服务器**方式调用此 URL。

> ⚠️ **直接在浏览器打开此 URL 会显示 Cloudflare 错误（1033）。** 这是正常现象 — 详见下方[常见问题排查](#zh-trouble)。

</details>

<details>
<summary>📖 <b>方式 B — 使用你自己的 Spotify 凭证（更稳定）</b></summary>

1. 前往 [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard) 并登录
2. 点击 **Create App**，填写 App Name（任意），Redirect URI：`http://localhost`，勾选 **Web API** → **Save**
3. 打开应用 → **Settings** → 复制 **Client ID** 和 **Client Secret**

```javascript
spotify: {
  enabled: true,
  clientId: '你的_CLIENT_ID',
  clientSecret: '你的_CLIENT_SECRET',
  externalAuthUrl: '',
  market: 'US'
}
```

</details>

---

<a id="zh-optional"></a>

### 🎧 可选音频源

<details><summary><b>Apple Music</b> — 自动获取 token</summary>

```javascript
applemusic: { enabled: true, mediaApiToken: 'token_here', market: 'US' }
```
</details>

<details><summary><b>Tidal</b> — 通过 Google 账号自动登录</summary>

```javascript
tidal: { enabled: true, token: 'token_here', countryCode: 'US' }
```
</details>

<details><summary><b>Deezer</b> — ARL cookie 用于高品质/无损</summary>

```javascript
deezer: { enabled: true /*, arl: '你的_ARL' */ }
```
</details>

<details><summary><b>Bilibili</b> — SESSDATA 用于 4K+/高级内容</summary>

```javascript
bilibili: { enabled: true, sessdata: '你的_SESSDATA' }
```
</details>

<details><summary><b>Pandora</b> — 社区 token 提供商</summary>

```javascript
pandora: { enabled: true, remoteTokenUrl: 'http://get.1lucas1apk.fun/pandora/gettoken' }
```
浏览器打开同样会出现 1033 错误 — 因为这是服务器对服务器的 API。
</details>

---

<a id="zh-run"></a>

### ▶️ 如何运行

```bash
npm install && npm run start

npm install -g pm2
pm2 start npm --name "NodeLink" -- run start
pm2 save && pm2 startup
```

**Pterodactyl / 面板托管：**

```javascript
import './dist/src/index.js';
```

面板 → **Startup** 标签 → 将启动文件改为 `run.js` → 点击**启动**。

---

<a id="zh-bot"></a>

### 🤖 连接 Discord 机器人

```javascript
const nodes = [{
  name: 'MyNodeLink',
  host: 'your.server.domain.com',
  port: 2333,
  password: 'your_password',
  secure: false
}];
```

---

<a id="zh-cluster"></a>

### ⚙️ 集群与性能调优

| 内存 | `workers` | `microWorkers` |
|---|---|---|
| 512 MB | `1` | `1` |
| 1 GB | `0`（自动） | `2` |
| 2 GB+ | `0`（自动） | `2–4` |

---

<a id="zh-trouble"></a>

### 🛠️ 常见问题排查

**在浏览器打开 `externalAuthUrl` / `remoteTokenUrl` 时出现 "Cloudflare Tunnel error 1033"**

这是**正常现象，不是 bug**，由两个原因共同造成：

1. **这些 URL 是 API 端点，不是网页。** 它们专为 NodeLink 设计，返回的是原始 JSON，没有 HTML 页面可渲染，所以直接访问看起来像是坏的，但对 NodeLink 来说运作正常。
2. **错误 1033 具体表示服务提供方服务器上的 Cloudflare Tunnel（`cloudflared`）当前离线或无法访问。** 这是对方服务器的临时故障，与你的 NodeLink 配置无关。如果 NodeLink 本身也开始获取 token 失败（日志中出现 `[ERROR]`），说明服务确实宕机了 — 此时请切换到**方式 B（使用自己的 Spotify 凭证）**直到恢复。

<div align="right"><a href="#zhongwen">⬆ 返回顶部</a></div>

---

<br/>

<div align="center">

---

**Built with ♥️ by [iamprmgvyt](https://github.com/iamprmgvyt)**

[🇬🇧 English](#english) &nbsp;·&nbsp; [🇻🇳 Tiếng Việt](#tieng-viet) &nbsp;·&nbsp; [🇫🇷 Français](#francais) &nbsp;·&nbsp; [🇮🇳 हिन्दी](#hindi) &nbsp;·&nbsp; [🇨🇳 中文](#zhongwen)

</div>
