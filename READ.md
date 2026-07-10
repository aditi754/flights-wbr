# Flights · Weekly Business Review

Live weekly business-review dashboard for the OnArrival Flights Pod.

**Live URL:** https://aditi754.github.io/flights-wbr/

## Auto-refresh schedule

Every **Friday 17:00 IST**, an automated Cowork task pulls fresh numbers from:

- **PostHog** — searches, unique searchers, aggregate user-days, funnel steps 1–6
- **Satori** — bookings + unique bookers across 6 org schemas (Jupiter, Niyo, UNI, OneCard, Cheq, Novio)
- **PostHog (Kiwi fallback)** — Kiwi bookings via PayConfirm_Success events (Kiwi has no Satori schema)
- **Linear** — In Progress / In Review / QA / Done items for the Flights Pod team

The refresh edits the SNAPSHOT block in `index.html` and republishes. GitHub Pages redeploys within ~1 minute.

## Window

- **This week:** Fri 17:30 IST → next Fri 17:30 IST
- **Last week:** the prior 7-day window (also Fri-anchored)

## Coverage — 7 organizations

Niyo Global · Jupiter · UNI (UniCards) · OneCard · Cheq · Credilio (= Novio in Satori) · Kiwi (Gokiwi)

## Metric definitions

- **Total searches** — count of `Home_Search` events across all 7 orgs (PostHog). Every fire counts.
- **Unique searchers** — `uniq(person_id)` on `Home_Search`. A user who searches 200 times = 1.
- **Aggregate user-days** — distinct `(person_id, IST-date)` pairs. A user active on 3 different days = 3. A user active 10 times in one day = 1.
- **Bookings** — total flight orders with `PAYMENT_SUCCESSFUL` payment and `bookingStatus != 'ITINERARY'`, across 6 Satori org schemas + Kiwi from PostHog. **Cancellations still count** — this is gross bookings, not net-of-cancellations. Refunds don't reduce the count.
- **Unique bookers** — distinct `userId` who made ≥ 1 successful booking. A user who books 3 times = 1.
- **Booking conversion** — total bookings ÷ aggregate user-days. Delta shown in absolute percentage points (pp), not relative %.

## Funnel

Steps 1–6 come from PostHog `uniq(person_id)` per event, across all 7 orgs. Step 7 (PayConfirm_Success) comes from **Satori total bookings + Kiwi PostHog fallback** — this is the source-of-truth booking count. Unit at step 7 is orders, not unique users (one user can book multiple times).

Two orgs need special handling:
- **UNI has Satori data but doesn't fire PostHog `PayConfirm_Success`** → their bookings come from Satori.
- **Kiwi has PostHog events but no Satori schema** → their bookings come from PostHog `PayConfirm_Success`.

## Manual refresh

Ping @aditi in Cowork and say "refresh WBR" — the same automation runs on-demand and the live URL updates within a minute.

## Data sources at a glance

| Metric | Source | Notes |
|---|---|---|
| Total searches | PostHog | All 7 orgs |
| Unique searchers | PostHog | All 7 orgs |
| Aggregate user-days | PostHog | All 7 orgs, IST-day boundaries |
| Funnel steps 1–6 | PostHog | Unique users per step |
| Bookings | Satori + PostHog (Kiwi) | Source of truth. Gross, not net-of-cancellations. |
| Unique bookers | Satori + PostHog (Kiwi) | Distinct userId |
| Linear pipeline | Linear | Flights Pod team, refreshed every WBR run |

## Historical snapshots

Every Friday commit is a natural snapshot — use `git log` on this repo to browse historical WBRs.
