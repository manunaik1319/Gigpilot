# GigPilot 🚀

> **Earn more. Switch less.**

GigPilot is a smart Android assistant that helps delivery partners and bike taxi riders manage multiple gig apps efficiently — reducing app switching, surfacing the best opportunities, and maximising daily earnings.

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Android-brightgreen.svg)](https://android.com)
[![Language](https://img.shields.io/badge/Language-Kotlin-orange.svg)](https://kotlinlang.org)
[![Status](https://img.shields.io/badge/Status-Pre--build-blue.svg)]()

---

## The Problem

Gig workers in India often run multiple apps simultaneously — Uber, Ola, Rapido, Swiggy, Zomato, Blinkit — and face a set of daily frustrations that quietly eat into their earnings:

- Constant, exhausting app switching
- Missing better-paying orders while accepting worse ones
- Slow response time due to notification overload
- No clear view of what they're actually earning across platforms
- Mental fatigue from decision-making under time pressure

The average delivery partner makes dozens of accept/reject decisions every shift. GigPilot makes each one faster and smarter.

---

## The Solution

GigPilot acts as a **smart control layer on top of all gig apps** — not a replacement, but an assistant. It aggregates, analyses, and recommends so riders can focus on riding.

```
Without GigPilot          With GigPilot
─────────────────         ─────────────────────────────
Swiggy notification  →    ┌─────────────────────────┐
Uber notification    →    │  🔥 Best: Rapido ₹85    │
Rapido notification  →    │  Swiggy ₹60 · 0.4 km   │
                          │  Uber ₹72 · 1.2 km      │
*rider checks 3 apps*     │  [Open Rapido]           │
*misses best order*       └─────────────────────────┘
                          *one tap, done*
```

---

## Features

### 🔔 Unified Notification Dashboard
Collects ride and order alerts from all active gig apps into a single, scannable screen. No more alt-tabbing between apps while a timer runs out.

### 🧠 Smart Recommendation Engine
Ranks incoming opportunities in real time based on:
- Estimated payout
- Pickup distance
- Time of day and shift context
- Historical acceptance patterns

```
Rapido  →  ₹85  ·  0.8 km  ·  ⭐ Recommended
Swiggy  →  ₹60  ·  0.4 km
Uber    →  ₹72  ·  1.2 km
```

### ⚡ One-Tap App Launch
Instantly deep-links into the correct app to accept the recommended request — no hunting through the home screen.

### 💰 Cross-App Earnings Tracker
Unified daily, weekly, and monthly income view across all platforms.

```
Today's earnings
─────────────────
Uber      ₹400
Swiggy    ₹500
Rapido    ₹300
──────────────
Total     ₹1,200
```

### ⛽ Expense & Profit Calculator
Track fuel, maintenance, and other costs to see your **real** take-home.

```
Earnings   ₹1,200
Fuel        − ₹300
────────────────────
Net profit   ₹900
```

### 🤖 Auto-Accept (Experimental)
Rule-based automatic order acceptance using Android Accessibility Service. Configurable filters: minimum payout, maximum distance, specific apps.

> ⚠️ **Use with caution.** Auto-accept operates in a grey area with gig platform terms of service. It is disabled by default and not part of the core product experience. Enable only if you understand the risks.

---

## Architecture

```
┌─────────────────────────────────────────┐
│         Gig App Notifications           │
│   (Uber · Ola · Rapido · Swiggy · ...)  │
└────────────────┬────────────────────────┘
                 │
         ┌───────▼────────┐
         │  Notification  │   NotificationListenerService
         │    Listener    │
         └───────┬────────┘
                 │
         ┌───────▼────────┐
         │  Offer Parser  │   Extracts payout, distance, app
         └───────┬────────┘
                 │
         ┌───────▼────────┐
         │    Decision    │   Ranks offers, applies rules
         │    Engine      │
         └───────┬────────┘
                 │
         ┌───────▼────────┐
         │   Overlay UI   │   Foreground overlay card
         └───────┬────────┘
                 │
         ┌───────▼────────┐
         │  App Launcher  │   Deep-links into gig app
         └────────────────┘
```

---

## Tech Stack

### Android (Core)
| Layer | Technology |
|---|---|
| Language | Kotlin |
| UI | Jetpack Compose + XML |
| Notification capture | `NotificationListenerService` |
| Overlay | `WindowManager` + `TYPE_APPLICATION_OVERLAY` |
| Automation | `AccessibilityService` |
| Background | `ForegroundService` |
| Usage stats | `UsageStatsManager` |

### Backend (Optional / Phase 2)
| Component | Technology |
|---|---|
| API | Node.js / Firebase Functions |
| Database | Firestore or PostgreSQL |
| Auth | Firebase Auth |
| Analytics | Custom event pipeline |

---

## Project Structure

```
com.gigpilot/
│
├── notification/          # NotificationListenerService, parsers
│   ├── NotificationService.kt
│   ├── OfferParser.kt
│   └── models/
│
├── automation/            # AccessibilityService, auto-accept logic
│   ├── GigAccessibilityService.kt
│   └── AutoAcceptEngine.kt
│
├── rules/                 # Decision engine, ranking logic
│   ├── DecisionEngine.kt
│   ├── RankingStrategy.kt
│   └── RuleConfig.kt
│
├── data/                  # Room DB, repositories, models
│   ├── AppDatabase.kt
│   ├── EarningsRepository.kt
│   └── models/
│
├── ui/                    # Compose screens, overlay, widgets
│   ├── overlay/
│   ├── dashboard/
│   ├── earnings/
│   └── settings/
│
└── utils/                 # App launcher, helpers, extensions
    ├── AppLauncher.kt
    └── Extensions.kt
```

---

## Permissions Required

| Permission | Purpose |
|---|---|
| `BIND_NOTIFICATION_LISTENER_SERVICE` | Read notifications from all apps |
| `SYSTEM_ALERT_WINDOW` | Show overlay on top of other apps |
| `BIND_ACCESSIBILITY_SERVICE` | Auto-accept (experimental only) |
| `FOREGROUND_SERVICE` | Keep listener running in background |
| `PACKAGE_USAGE_STATS` | Detect which app is in foreground (optional) |

> All permissions must be granted manually by the user via Android Settings. GigPilot never requests permissions silently.

---

## Getting Started

### Prerequisites
- Android Studio Hedgehog or later
- Android SDK 26+ (Android 8.0 minimum)
- Kotlin 1.9+

### Setup

```bash
# Clone the repository
git clone https://github.com/yourusername/gigpilot.git
cd gigpilot

# Open in Android Studio and sync Gradle
./gradlew build
```

### First Run
1. Grant **Notification Access** in Settings → Apps → Special App Access
2. Grant **Display Over Other Apps** permission
3. Optionally enable **Accessibility Service** for auto-accept (experimental)
4. Add your active gig apps in the GigPilot dashboard

---

## Known Limitations

| Limitation | Impact |
|---|---|
| No official gig app APIs | Relies on notification text parsing — brittle if app UI changes |
| UI change fragility | A Swiggy or Uber update can break the offer parser |
| Platform ToS risk | Auto-accept may violate gig platform terms of service |
| Device variance | Overlay behaviour differs across Android OEM skins (MIUI, One UI) |
| Battery impact | Foreground service increases background power usage |

---

## Roadmap

### v1.0 — MVP
- [x] Architecture design
- [ ] NotificationListenerService with parsers for Swiggy, Zomato, Uber, Rapido
- [ ] Basic overlay UI with ranked offers
- [ ] Earnings tracker (manual entry)
- [ ] One-tap app launch

### v1.5 — Smart Layer
- [ ] ML-based offer ranking
- [ ] Fuel expense logging
- [ ] Shift summary with net profit
- [ ] WhatsApp share for daily earnings

### v2.0 — Insights
- [ ] Zone heatmaps (best areas by time)
- [ ] AI earning predictions
- [ ] Shift planning suggestions
- [ ] Fleet/multi-rider dashboard (B2B)

### Future
- [ ] Community insights (anonymised, opt-in)
- [ ] Embedded financial tools (savings, insurance referrals)
- [ ] Partner API integrations

---

## Design Philosophy

GigPilot is built on one core principle:

> **Assist the rider. Never replace their judgment.**

The app surfaces information faster and filters noise — but the rider always makes the final call. Auto-accept is experimental and off by default. The goal is to reduce cognitive load, not automate away the human.

Every feature is evaluated against a single question: *does this help a rider earn more with less stress?*

---

## Contributing

Contributions are welcome. Here's how to get involved:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/offer-parser-zomato`
3. Commit your changes: `git commit -m 'Add Zomato notification parser'`
4. Push and open a Pull Request

For major changes, please open an issue first to discuss what you'd like to change. See [CONTRIBUTING.md](CONTRIBUTING.md) for full guidelines.

### Good First Issues
- Add a new gig app parser (Blinkit, Porter, Dunzo)
- Improve overlay UI for small screens
- Write unit tests for the Decision Engine
- Translate UI strings to Hindi, Tamil, Telugu

---

## Business Model

GigPilot uses a freemium model designed around the Indian gig economy:

| Tier | Price | Features |
|---|---|---|
| Free | ₹0 | Basic dashboard, earnings log, 3 app support |
| Pro | ₹9–₹49/mo | Smart recommendations, all apps, analytics, fuel tracker |
| Fleet | ₹50–₹100/mo | Multi-rider dashboard, aggregated insights, priority support |

Revenue streams also include affiliate partnerships (fuel apps, insurance, microloans) and anonymised aggregate data insights for logistics and urban planning use cases.

---

## License

This project is licensed under the [MIT License](LICENSE).

---

## Support & Community

- **GitHub Issues** — Bug reports and feature requests
- **Discussions** — Ideas, questions, roadmap feedback
- **Star the repo** if GigPilot solves a real problem for you ⭐

---

*Built for the riders who make India's delivery economy run.*
