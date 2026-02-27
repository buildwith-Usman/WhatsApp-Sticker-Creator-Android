# WhatsApp Sticker Maker — Android

A feature-rich Android application that lets users discover, customize, and send sticker packs directly to WhatsApp. Users can browse curated sticker packs from an online catalogue, build their own stickers using a full-featured image editor, and push any pack to WhatsApp with a single tap.

---

## Table of Contents

- [Screenshots](#screenshots)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture Overview](#architecture-overview)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup & Configuration](#setup--configuration)
- [Build Instructions](#build-instructions)
- [Key Integrations](#key-integrations)
- [WhatsApp Sticker Protocol](#whatsapp-sticker-protocol)
- [Monetisation](#monetisation)
- [Contributing](#contributing)
- [License](#license)

---

## Screenshots

### Promotional

| | | | |
|:---:|:---:|:---:|:---:|
| ![Sticker Packs](screenshots/promo_01_sticker_packs.webp) | ![Share Stickers](screenshots/promo_02_share_stickers.webp) | ![Couple Stickers](screenshots/promo_03_couple_stickers.webp) | ![Romantic Stickers](screenshots/promo_04_romantic_stickers.webp) |
| My Love Stickers | Share Stickers | Couple Stickers | Romantic Stickers |
| ![Create Stickers](screenshots/promo_05_create_stickers.webp) | ![Categories](screenshots/promo_06_categories.webp) | ![Animated Stickers](screenshots/promo_07_animated_stickers.webp) | |
| Create Your Own | Sticker Categories | Animated Stickers | |

---

### App Screens

| | | | |
|:---:|:---:|:---:|:---:|
| ![Splash](screenshots/01_splash.jpeg) | ![Home](screenshots/02_home.jpeg) | ![Navigation Drawer](screenshots/03_navigation_drawer.jpeg) | ![Pack Detail](screenshots/04_pack_detail.jpeg) |
| Splash | Home | Navigation Drawer | Pack Detail |
| ![Pack Detail 2](screenshots/05_pack_detail_2.jpeg) | ![Create Sticker](screenshots/06_create_sticker.jpeg) | ![Add Sticker Pack](screenshots/07_add_sticker_pack.jpeg) | ![Premium](screenshots/08_premium.jpeg) |
| Pack Detail | Create Sticker | Add Sticker Pack | Premium |
| ![Daily Reward](screenshots/09_daily_reward.jpeg) | ![Spin Wheel](screenshots/10_spin_wheel.jpeg) | ![Love Memory](screenshots/11_love_memory.jpeg) | ![Love Tester](screenshots/12_love_tester.jpeg) |
| Daily Reward | Spin Love Wheel | Love Memory | Love Tester |
| ![Settings](screenshots/13_settings.jpeg) | | | |
| Settings | | | |

---

## Features

| Category | Details |
|---|---|
| **Sticker Packs** | Browse categorised packs served from a REST backend; search by tag or keyword |
| **Image Editor** | Crop, rotate, paint, add text overlays, and layer sticker assets onto any photo |
| **Background Remover** | Cutout tool to isolate subjects from images |
| **WhatsApp Integration** | Native Content Provider exposes packs to WhatsApp via the official sticker API |
| **Authentication** | Firebase Auth (phone OTP + Google Sign-In + Facebook Login) |
| **Push Notifications** | Firebase Cloud Messaging for pack updates and promotions |
| **In-App Purchases** | Google Play Billing for premium pack unlocks |
| **Ads** | AdMob, AppLovin MAX (with AdColony, Facebook Audience Network, InMobi, Unity Ads mediation) |
| **Onboarding** | Intro/slide screens with ViewPager |

---

## Tech Stack

### Language & Platform
- **Java** (source/target compatibility 1.8)
- **Android SDK** — minSdk 21 (Lollipop) / targetSdk 31 (Android 12)
- **Gradle 7.1.2** build system

### Networking
| Library | Purpose |
|---|---|
| Retrofit 2.7.2 | Type-safe REST client |
| OkHttp 4.9.2 | HTTP engine + logging interceptor |
| Gson 2.8.6 | JSON serialisation/deserialisation |

### Image Loading & Editing
| Library | Purpose |
|---|---|
| Glide 4.11.0 + WebP decoder | Efficient image loading & caching |
| Picasso 2.71828 | Supplementary image loading |
| Android Image Cropper 2.8.0 | Crop UI |
| Gesture Views 2.5.2 | Pinch-zoom on editor canvas |
| EasyImage 1.3.1 | Image picker abstraction |
| FishBun 0.8.7 | Multi-image gallery picker |
| CheckerboardDrawable 1.0.2 | Transparent-background visualisation |

### Firebase
- `firebase-auth` — Phone, Google, Facebook authentication
- `firebase-core` + `firebase-analytics` — Analytics
- `firebase-messaging` — Push notifications
- `firebase-ads` (AdMob) — Ad serving

### UI
| Library | Purpose |
|---|---|
| Material Components 1.5.0 | Material Design widgets |
| ConstraintLayout 2.1.3 | Responsive layouts |
| RecyclerView 1.2.1 | List/grid displays |
| ViewPager2 | Intro slides & tab paging |
| BubbleNavigation 1.0.7 | Bottom navigation bar |
| NavigationTabBar 1.2.5 | Animated tab bar |
| MaterialSearchView 1.4.2 | Search UI |
| Toasty 1.4.2 | Styled toast messages |
| ViewPagerIndicator | Page-dot indicators |
| CountryCodePicker 2.4.1 | Phone number input |
| Shape ImageView 0.9.3 | Circle/rounded avatars |

### Storage & Persistence
- **Hawk 2.0.1** — Encrypted SharedPreferences wrapper
- **MultiDex 2.0.1** — Method count > 64K support

### Ads Mediation (AppLovin MAX)
- AppLovin SDK 11.2.2
- AdColony adapter
- Google / Google Ad Manager adapter
- Facebook Audience Network adapter
- InMobi adapter
- Unity Ads adapter

### Billing
- Google Play Billing 4.1.0

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                   Presentation Layer                │
│  Activities / Fragments  ←→  Adapters / ViewHolders │
└───────────────────┬─────────────────────────────────┘
                    │
┌───────────────────▼─────────────────────────────────┐
│                    Data Layer                       │
│  Retrofit (apiRest)  ←→  apiClient (OkHttp)         │
│  Entity models  ←→  Gson converters                 │
└───────────────────┬─────────────────────────────────┘
                    │
┌───────────────────▼─────────────────────────────────┐
│                 Provider Layer                      │
│  StickerContentProvider  (WhatsApp sticker API)     │
└─────────────────────────────────────────────────────┘
```

The app follows a **layered architecture** with clear separation between UI, data access, and the WhatsApp integration layer.

---

## Project Structure

```
app/src/main/
├── java/com/virmana/stickers_app/
│   ├── adapter/          # RecyclerView adapters (Category, Sticker, User, Tag, …)
│   ├── api/              # Retrofit client & REST interface
│   ├── config/           # App-wide constants (Config.java)
│   ├── editor/           # Image editor module
│   │   ├── editimage/    # Edit activity, fragments, views, tasks, utils
│   │   └── picchooser/   # Custom gallery/bucket picker
│   ├── entity/           # API response models
│   ├── Manager/          # PrefManager (shared prefs helper)
│   ├── model/            # Internal data models
│   ├── provider/         # StickerContentProvider (WhatsApp IPC)
│   ├── services/         # FCM service, Billing service
│   ├── task/             # Async tasks
│   └── ui/               # All user-facing Activities
│       ├── SplashActivity
│       ├── HomeActivity
│       ├── CategoryActivity
│       ├── SearchActivity
│       ├── LoginActivity
│       ├── EditorActivity
│       ├── EditActivity
│       ├── SettingsActivity
│       └── … (10+ more)
├── assets/
│   ├── fonts/            # Custom TTF fonts (10 fonts)
│   └── stickers/         # Bundled sticker assets (type1–type6)
└── res/                  # Layouts, drawables, strings, XML configs
```

---

## Prerequisites

- **Android Studio** Chipmunk (2021.2.1) or later
- **JDK 11** or later
- **Android SDK** with API level 21–31 installed
- A **Firebase project** (for auth, FCM, and AdMob)
- A **Facebook Developer** app (for Facebook Login and Audience Network)
- An **AppLovin** account (for MAX mediation)
- A **Google Play Console** account (for billing testing)

---

## Setup & Configuration

### 1. Clone the repository

```bash
git clone https://github.com/<your-org>/whatsapp-sticker-maker-android.git
cd whatsapp-sticker-maker-android
```

### 2. Firebase

1. Create a project at [console.firebase.google.com](https://console.firebase.google.com).
2. Register the Android app with package name `com.virmana.stickers_app`.
3. Download `google-services.json` and place it at `app/google-services.json`.
4. Enable **Phone**, **Google**, and **Facebook** sign-in providers in Authentication settings.
5. Register your SHA-1 fingerprint for Google Sign-In.

### 3. Facebook SDK

In `app/src/main/res/values/strings.xml` (or `facebook.xml`), set:

```xml
<string name="facebook_app_id">YOUR_FACEBOOK_APP_ID</string>
<string name="fb_login_protocol_scheme">fbYOUR_FACEBOOK_APP_ID</string>
```

### 4. AppLovin MAX

Replace the SDK key in `AndroidManifest.xml`:

```xml
<meta-data
    android:name="applovin.sdk.key"
    android:value="YOUR_APPLOVIN_SDK_KEY" />
```

### 5. AdMob App ID

Replace the AdMob application ID in `AndroidManifest.xml`:

```xml
<meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="ca-app-pub-XXXXXXXXXXXXXXXX~XXXXXXXXXX" />
```

### 6. Backend API

Update the base URL in `app/src/main/java/com/virmana/stickers_app/config/Config.java`:

```java
public static final String BASE_URL = "https://your-api-server.com/";
```

### 7. Local properties

Create or update `local.properties` at the project root:

```properties
sdk.dir=/path/to/your/Android/sdk
```

> **Note:** `local.properties` and `google-services.json` are excluded from version control via `.gitignore`.

---

## Build Instructions

```bash
# Debug build
./gradlew assembleDebug

# Release build (requires signing config)
./gradlew assembleRelease

# Run unit tests
./gradlew test

# Run instrumented tests
./gradlew connectedAndroidTest

# Clean build artifacts
./gradlew clean
```

The generated APK/AAB will be located at:
- Debug: `app/build/outputs/apk/debug/`
- Release: `app/build/outputs/apk/release/`

---

## Key Integrations

### WhatsApp Sticker Protocol

Sticker packs are exposed to WhatsApp via a `ContentProvider` (`StickerContentProvider`) that implements the WhatsApp sticker API contract:

- **Authority:** `com.virmana.stickers_app.provider.StickerContentProvider`
- **Read permission:** `com.whatsapp.sticker.READ`
- Pack metadata is queried by WhatsApp and listed in its sticker browser.
- Each sticker must be a 512×512 WebP file ≤ 100 KB.
- Every pack requires a minimum of 3 stickers and a maximum of 30.

Refer to the official [WhatsApp Stickers GitHub](https://github.com/WhatsApp/stickers) for protocol details.

### Image Editor

The custom editor (`EditImageActivity`) supports:
- **Crop** — free-form and ratio-locked via `CropImageView`
- **Rotate** — 90° steps via `RotateImageView`
- **Paint** — freehand drawing on `CustomPaintView`
- **Text** — font selection (10 bundled fonts) + colour picker
- **Sticker overlay** — layer bundled sticker assets
- **Undo/Redo** — `RedoUndoController` with `EditCache`

---

## Monetisation

| Mechanism | SDK |
|---|---|
| Banner / Interstitial / Rewarded Ads | AdMob (Google Mobile Ads) |
| Mediated Ads | AppLovin MAX |
| Audience Network | Facebook |
| In-App Purchases | Google Play Billing 4.1.0 |

Billing logic is encapsulated in `BillingSubs.java` and `CallBackBilling.java` under the `services` package.

---

## Contributing

1. Fork the repository and create a feature branch from `main`.
2. Follow the existing code style (Java, no Kotlin migration planned).
3. Ensure all lint checks pass: `./gradlew lint`.
4. Open a pull request with a clear description of the change and its motivation.

---

## License

This project is proprietary. All rights reserved. Unauthorised copying, modification, distribution, or use of this software, in whole or in part, without express written permission is strictly prohibited.
