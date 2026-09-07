# 📱 Running Sahakari Seva on Android via Expo Go

Sahakari Seva is a **pure Expo (SDK 52) application** — every dependency used
(`expo-location`, `expo-linear-gradient`, `expo-haptics`, `react-native-webview`,
`@react-native-async-storage/async-storage`, `react-native-svg`, etc.) is
**bundled inside Expo Go**, so there is **no Android Studio / native build
required**. You can run the full app on any Android phone in under 5 minutes.

---

## ✅ Prerequisites

| Requirement | Notes |
|---|---|
| Android phone (Android 8.0+ recommended) | Any modern device works |
| **Expo Go** app | Install from the Play Store (free) |
| Computer with Node.js ≥ 18 and npm | To run the Metro dev server |
| Phone + computer on the **same Wi-Fi** | Required for the LAN connection (or use Tunnel mode, see below) |

---

## 🚀 Step-by-step (Android)

### Step 1 — Install Expo Go on your phone

Open the **Google Play Store** on your Android phone and search for:

```
Expo Go
```

Install it (publisher: **Expo**). It is a small app (~30 MB) that can run any
Expo project — no build, no APK needed.

### Step 2 — Start the app on your computer

Open a terminal in the `mobile/` folder:

```bash
cd mobile
npm install        # only the first time
npm start          # starts the Metro bundler
```

You will see a QR code in the terminal.

> 💡 Tip: press `a` in the terminal to auto-open on an Android emulator, or
> scan the QR code with the Expo Go app (below).

### Step 3 — Connect the phone

1. Make sure your phone and computer are on the **same Wi-Fi network**.
2. Open the **Expo Go** app on your phone.
3. Tap **"Scan QR code"** inside Expo Go and scan the QR code from the terminal.
4. The app bundles and opens automatically. 🎉

> If you use a **VPN** or the network blocks local connections, press
> **`Shift + S`** in the Metro terminal to switch to **Tunnel mode**, then scan
> the new QR code — this works over the internet and even on mobile data.

### Step 4 — (Optional) Connect to the live backend API

By default the app runs fully **offline with demo data** (fallback records are
built into the app, so every screen — booking, payments, AI forecast,
allocation — still works).

To use the real Express backend from your phone:

1. Start the backend on your computer:
   ```bash
   cd backend
   npm install
   npm run dev      # serves on http://localhost:5001
   ```
2. Find your computer's **LAN IP** (Windows: `ipconfig`, macOS/Linux: `ipconfig getifaddr en0`).
3. Start Expo with the API URL pointed at that IP:
   ```bash
   # from the mobile/ folder, on the same Wi-Fi
   EXPO_PUBLIC_API_URL=http://<YOUR-COMPUTER-IP>:5001 npm start
   ```
   The app's API client also **auto-detects** the computer's IP from the Expo
   Go connection, so in most cases this step is not even required.

---

## 🌐 Switching languages (try it!)

The app ships with **English + 7 Indian languages**:

| Language | Native name |
|---|---|
| English | English |
| Hindi | हिन्दी |
| Bengali | বাংলা |
| Tamil | தமிழ் |
| Telugu | తెలుగు |
| Marathi | मराठी |
| Gujarati | ગુજરાતી |
| Kannada | ಕನ್ನಡ |

Tap the **🌐 language chip** (top-right of any screen) to open the language
picker. The whole app — tabs, buttons, alerts, invoices, admin dashboards —
switches **simultaneously** behind a smooth branded cross-fade, with **zero
glitches**. Your choice is remembered the next time you open the app.

---

## 🛠 Troubleshooting

| Problem | Fix |
|---|---|
| QR code won't scan | Widen the terminal window, or press `c` to show the QR again |
| "Connection to the dev server failed" | Both devices must be on the same Wi-Fi; try `Shift + S` (Tunnel mode) |
| App loads but API calls fail | Expected if the backend isn't running — the app shows cached demo data automatically |
| Black screen on old phones | Update Expo Go from the Play Store (must match SDK 52) |
| Location prompt doesn't appear | Android requires Location permission — grant it when prompted, or use "Select Area Manually" |

---

## 🔁 Re-running later

```bash
cd mobile
npm start
```

Scan the QR with Expo Go again — that's it. The app starts instantly and
remembers your last language, role, and demo session.