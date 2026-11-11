# Kalshi Whale Watch — Native Android App + FCM Backend

A native Android (Kotlin + Jetpack Compose) real-time whale trade monitor for **Kalshi Sports & Combo Markets** featuring a dark gaming HUD aesthetic (`#080B12` obsidian background, `#00FF87` neon emerald YES signals, `#FF2A5F` crimson NO signals).

## Key Capabilities Built In
1. **$500+ Whale Trade & 1.5s Order-Book Sweep Engine**:
   - Calculates real-time taker notional outlay: `count * (taker_side_price / 100.0) >= $500`.
   - Aggregates split fills occurring on the **same `ticker` + `taker_side` within 1,500ms** so a $1,200 order sweeping thin sports books across multiple fills still fires a verified Whale Sweep Alert.
   - Deduplicates via an LRU `trade_id` and sweep signature cache so your phone never double-buzzes.
2. **Dual Background Alert Architecture (Works Immediately + With App Closed)**:
   - **Mode A — Standalone Live Sentinel Foreground Service (Zero Backend Required)**: Runs a persistent Android Foreground Service (`WhaleSentinelService`) directly on your phone that polls Kalshi's public `/trade-api/v2/markets/trades` and `/markets` endpoints and triggers high-priority heads-up notifications even when the screen is off.
   - **Mode B — Firebase Cloud Messaging (FCM) Push Server (`/backend-fcm-worker`)**: A Node.js worker that monitors Kalshi 24/7 in the cloud and dispatches instant FCM high-priority wake-up pushes when the app is completely force-closed.
3. **4 Gaming-Style HUD Screens**:
   - **WHALE ALERTS ($500+)**: Live feed of single-print whales and multi-fill book sweeps with threshold slider ($500 default) and test alert trigger.
   - **LIVE SPORTS TAPE**: Real-time sports market prices, volume surges, and recent tape filtered by NFL, NCAAF, NBA, UFC, MLB, NHL, and Soccer.
   - **COMBO / PARLAYS**: Dedicated monitor for multivariate and multi-leg combo sports contracts.
   - **WATCHLIST & SENTINEL**: Pin favorite tickers, toggle the background Sentinel service, and run a 1-tap Diagnostic Self-Test.

---

## Option 1: Zero-Install Cloud APK Build via GitHub Actions (Recommended — 6 Mins)
1. Create a new repository on GitHub (can be Private or Public).
2. Upload/push the contents of this ZIP into the repository root.
3. Click the **Actions** tab in your GitHub repository -> Select **Build Kalshi Whale Watch APK**.
4. Once the green checkmark finishes (~5 minutes), scroll down to **Artifacts** and download **`KalshiWhaleWatch-debug-apk.zip`**.
5. Extract `app-debug.apk` on your Android phone, tap it, allow "Install from Unknown Sources" if prompted, and launch **Kalshi Whale Watch**.

---

## Option 2: Local Compile in Android Studio
1. Open **Android Studio Ladybug (2024.2.1)** or newer.
2. Select **Open** and choose the `android-app` folder (or root folder where settings.gradle.kts lives).
3. Wait for Gradle sync to complete, then click **Build -> Build Bundle(s) / APK(s) -> Build APK(s)**.
4. Your installable APK will be generated at `app/build/outputs/apk/debug/app-debug.apk`.

---

## Option 3: Enabling the Cloud FCM Push Worker (Optional)
The app works out-of-the-box using its built-in **Live Sentinel Foreground Service**. If you also want cloud-pushed FCM alerts:
1. Create a free Firebase project at [console.firebase.google.com](https://console.firebase.google.com/).
2. Add an Android app with package name `com.kalshi.whalewatch`, download `google-services.json`, and place it in `app/google-services.json`.
3. Generate a Firebase Admin Service Account JSON key (`Project Settings -> Service Accounts -> Generate new private key`), save it as `backend-fcm-worker/serviceAccountKey.json`.
4. Run the backend worker:
   ```bash
   cd backend-fcm-worker
   npm install
   npm start
   ```
