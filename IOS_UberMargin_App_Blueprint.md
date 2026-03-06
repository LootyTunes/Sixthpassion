# iOS Uber Margin Copilot — Framework, Architecture, and Requirements

## 1) Product Goal
Build an iOS companion app that helps a driver predict profitable positioning and compute **per-ride gross margin** in near real time.

Core outcomes:
- Per-ride and per-hour gross margin visibility.
- Pre-surge positioning recommendations using predictive signals.
- Cost-aware decisions (fuel, deadhead miles, idle time).

## 2) Important Constraint (Uber Integration)
Uber Driver app does not offer broad public APIs for full ride lifecycle + fare/tip details in most markets. We should plan a layered ingestion strategy:

1. **Primary:** Driver-entered values + photo/OCR of trip receipt screens.
2. **Secondary:** CSV/manual exports if available in your market/account.
3. **Optional advanced:** Email parsing of trip summaries (with user consent).

This means we can ship a strong MVP **without** direct Uber API dependence.

## 3) Margin Model (Per Ride)
Use this baseline formula in-app:

Gross Margin (ride) = (Fare + Tip + Incentive Allocations) - (Variable Cost)

Where Variable Cost can be estimated two ways:
- **Mileage method (recommended):**
  Variable Cost = (Paid Miles + Deadhead Miles) * Cost Per Mile
- **Fuel-only method:**
  Fuel Cost = Miles / MPG * Local Gas Price

Cost Per Mile should include:
- Fuel
- Tires/maintenance reserve
- Depreciation reserve
- Optional insurance allocation

## 4) Forecasting "Be the Surge" Engine
Create a zone-based demand score updated every 5–15 minutes.

Demand Score inputs:
- Time-of-day and day-of-week historical pickup density.
- Event schedule proximity (stadiums, concerts, airports).
- Weather deltas (rain, temperature drops).
- Flight arrival pulses (airport markets).
- Current pickup wait-time observations (manual taps in MVP).

Output:
- Top 3 staging zones with confidence score.
- Move/Stay recommendation with deadhead penalty.

## 5) iOS App Modules
1. **Trip Capture Module**
   - Manual entry: fare, tip, miles, pickup/dropoff area, timestamp.
   - OCR mode: parse screenshot/receipt fields into trip form.

2. **Cost Engine Module**
   - Vehicle profile (MPG, maintenance reserve, depreciation).
   - Gas price source and location-based lookup.
   - Per-ride and daily gross margin calculations.

3. **Strategy Module**
   - Zone scoring and ranking.
   - Shift planner (pre-commit target windows and area sequence).
   - Alerts: "Relocate now", "Hold", "Avoid low-margin zone".

4. **Analytics Module**
   - Hourly margin heatmap.
   - Acceptance quality dashboard (high vs low margin rides).
   - Weekly backtest: predicted vs realized margin.

## 6) Suggested Tech Stack
- iOS: SwiftUI + Combine.
- Local data: Core Data or SQLite.
- Backend (optional for MVP): Supabase/Firebase for sync + auth.
- Forecasting service: lightweight Python/Node microservice (optional phase 2).
- Weather/events/flight data providers: pluggable adapters.

## 7) MVP Scope (4–6 weeks)
- Manual trip logging.
- Vehicle + cost profile setup.
- Local gas price integration.
- Per-ride gross margin calculator.
- Daily summary: gross revenue, variable cost, margin/hour.
- Basic rule-based reposition suggestions (time + zones).

## 8) Phase 2 Scope
- OCR-assisted receipt import.
- Backtesting engine by zone + hour.
- Event/weather-weighted demand model.
- Push notifications for reposition opportunities.

## 9) What I Need From You to Start
### Business/Operations Inputs
- City/metro area(s) you drive.
- Typical driving schedule (days/hours).
- Vehicle details: year/model, MPG (city/highway), current mileage.
- Your preferred cost model:
  - Fuel-only, or
  - Full variable cost per mile.
- Target thresholds:
  - Minimum acceptable margin per ride.
  - Minimum acceptable margin per active hour.

### Data Access Inputs
- What Uber data you can reliably access now:
  - in-app screens,
  - trip history exports,
  - email trip summaries.
- Permission to use OCR on screenshots.
- Whether you want cloud sync (multi-device).

### Product Preferences
- Do you want this as:
  - iOS-only local app, or
  - iOS + cloud dashboard.
- Notification aggressiveness (low/medium/high).
- Privacy preference (all local vs cloud-enhanced).

## 10) First Build Deliverables
- Product requirements doc (PRD) for MVP.
- Data schema (trip, shift, zone, cost profile).
- Margin calculator spec with formulas.
- Wireframes for: Trip Capture, Shift Dashboard, Zone Recommendations.
- Implementation backlog with milestones.

## 11) Risk Notes
- Direct Uber API integration may be limited or unavailable; design MVP to avoid dependency.
- Margin accuracy depends on data quality (miles, tips, deadhead capture).
- Forecast quality improves over 2–4 weeks of local historical data.
