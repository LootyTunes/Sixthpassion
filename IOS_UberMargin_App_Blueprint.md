# iOS Uber Margin Copilot — Tri-State (NYC, Westchester, NJ, CT)

## 1) Product Goal
Build an iOS companion app that helps a driver predict profitable positioning and compute **per-ride gross margin** in near real time for the NYC tri-state operating area.

Core outcomes:
- Per-ride and per-hour gross margin visibility.
- Pre-surge positioning recommendations using predictive signals.
- Cost-aware decisions (fuel, deadhead miles, idle time).
- Multi-user accessibility features so different drivers can use the app effectively.

## 2) Operating Profile (Configured From Your Inputs)
This plan is now configured to your stated inputs:

- **Market:** NYC metro + Westchester + NJ + CT (tri-state).
- **Vehicle:** Honda Pilot 2019.
- **MPG assumption for modeling:**
  - **Conservative default:** 22 MPG combined (typical AWD estimate).
  - **Range for scenario testing:** 20–23 MPG combined depending on trim, traffic, weather, tire pressure, and driving style.
- **Cost method:** both fuel-only and full variable-cost-per-mile models supported.
- **Data ingestion:** all available channels (manual entry, OCR, exports, email summary parsing).
- **Product posture:** design for accessibility across different users (multi-language readiness + inclusive UX + cloud sync option).

## 3) Important Constraint (Uber Integration)
Uber Driver app does not generally expose a broad public API for full ride lifecycle + fare/tip detail access in most markets. The app should use a layered ingestion strategy:

1. **Primary:** Driver-entered values + photo/OCR of trip receipt screens.
2. **Secondary:** CSV/manual exports where available.
3. **Advanced:** Email parsing of trip summaries (opt-in).

This supports a strong MVP without requiring direct Uber API access.

## 4) Margin Model (Per Ride)
### Base formula
Gross Margin (ride) = (Fare + Tip + Incentive Allocation) - Variable Cost

### Cost method A: Fuel-only
Fuel Cost = Total Miles / MPG * Local Gas Price

### Cost method B: Full variable cost (recommended for decisioning)
Variable Cost = (Paid Miles + Deadhead Miles) * Cost Per Mile

Cost Per Mile components:
- Fuel
- Tires/maintenance reserve
- Depreciation reserve
- Optional insurance allocation

### Dual-display requirement
Every ride view should show:
- Fuel-only margin
- Full-variable-cost margin
- Difference between the two (to avoid overestimating profitability)

## 5) “Highest Realistic Margin” Target Definition
Use a practical, defendable target framework instead of one fixed number:

- **Per-ride margin target:** top 20–25% of your own trailing 4-week rides (market-adjusted).
- **Per-active-hour margin target:** top quartile of your own historical hours by zone/time block.
- **Trip reject/accept rule:** avoid requests that project below your dynamic margin floor unless they reposition into a high-probability zone.

Initial default policy (before enough local history):
- Maintain positive full-variable-cost margin on every accepted trip.
- Prioritize zones/time blocks that consistently deliver above-median margin/hour.

## 6) Tri-State “Be the Surge” Forecast Engine
Create zone-level demand scoring updated every 5–15 minutes.

### Region segmentation
- **NYC core zones** (borough-level high-demand nodes).
- **Westchester commuter/event nodes.**
- **NJ inbound/outbound corridors + airport-linked zones.**
- **CT commuter corridors + return-trip risk zones.**

### Demand score inputs
- Time-of-day/day-of-week historical pickup density.
- Event proximity (stadiums, concerts, conventions).
- Weather deltas (rain, cold snaps).
- Airport/rail pulse effects where relevant.
- Current pickup wait-time observations.
- Cross-border deadhead risk (especially late-night return legs).

### Output
- Top 3 staging zones with confidence score.
- Move/Stay recommendation.
- Deadhead penalty estimate.
- “Do not chase” flag if expected reposition cost outweighs upside.

## 7) iOS App Modules
1. **Trip Capture Module**
   - Manual ride logging.
   - OCR extraction from screenshots/receipts.
   - Import from available exports/email summaries.

2. **Cost Engine Module**
   - Vehicle profile with Honda Pilot defaults preloaded.
   - MPG calibration flow (driver can tune from actual tank data).
   - Gas price lookup by current region.
   - Fuel-only + full-variable-cost margin computation.

3. **Strategy Module**
   - Tri-state zone ranking and route staging.
   - Shift planner by time block.
   - Alerts: Relocate/Hold/Avoid recommendations.

4. **Analytics Module**
   - Margin/hour heatmap by zone and time.
   - Acceptance quality dashboard.
   - Backtest: predicted vs realized margin.

5. **Accessibility & Multi-User Module**
   - Large text support, VoiceOver labels, color-safe palettes.
   - Language-ready UI architecture.
   - Profile switching (future) for multiple drivers per fleet/account.

## 8) Suggested Tech Stack
- iOS: SwiftUI + Combine.
- Data store: Core Data/SQLite local-first.
- Sync/auth (recommended for multi-user accessibility): Supabase or Firebase.
- Forecasting service (phase 2): lightweight Python/Node scorer.
- Integrations: weather/events/transport data adapters.

## 9) MVP Scope (4–6 Weeks)
- Manual trip logging + OCR assist.
- Honda Pilot default profile with editable MPG.
- Local gas price integration.
- Dual margin view (fuel-only + full-variable-cost).
- Daily dashboard: gross revenue, variable cost, margin/hour.
- Rule-based reposition suggestions for tri-state zones.
- Baseline accessibility support (dynamic type, VoiceOver labels, contrast-safe colors).

## 10) Phase 2 Scope
- Automated ingestion from exports/email summaries.
- Advanced backtesting by zone/hour/day type.
- Event/weather-weighted forecasting.
- Smarter relocation push notifications.
- Team/fleet multi-user analytics.

## 11) Data We Should Confirm Before Build Starts
- Pilot trim (FWD/AWD) and real-world MPG from your own fill-ups.
- Exact priority airports/terminals you target.
- Peak windows you want to dominate (weekday commute, nights, weekends, events).
- Risk tolerance (aggressive repositioning vs conservative fuel protection).
- Preferred privacy model (local-only or cloud-synced cross-device use).

## 12) First Build Deliverables
- MVP PRD tailored to NYC/Westchester/NJ/CT operations.
- Data schema: trip, shift, zone, cost profile, forecast snapshot.
- Margin calculator spec with both cost methods.
- Wireframes: Trip Capture, Shift Dashboard, Zone Recommendations, Accessibility settings.
- Milestone backlog with acceptance criteria.

## 13) Risk Notes
- Direct Uber API dependency should be avoided in MVP.
- Margin quality depends on accurate miles/tips/deadhead capture.
- Forecast quality improves after 2–4 weeks of local driving data.
- Cross-state toll/traffic effects can distort margin if not modeled explicitly; include these in later iterations.
