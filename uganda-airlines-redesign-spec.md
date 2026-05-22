# Uganda Airlines — Website Redesign Specification
> **For use by an AI coding agent (e.g. Impeccable / OpenCode)**
> This document is the single source of truth for the redesign. Follow every section closely.

---

## 1. Project Overview

**Client:** Uganda Airlines (Uganda National Airlines Company Limited — UNACL)
**Website:** ugandairlines.com
**Tagline:** "Explore the Pearl of Africa and Beyond"
**HQ:** Entebbe International Airport, Wakiso District, Uganda
**Phone:** +256 200 406400 | **Email:** reservations@ugandairlines.com
**Fleet:** Airbus A330-800 (wide-body) + CRJ900 aircraft — youngest fleet in Africa (2021–2024)

**Goal:** Redesign the Uganda Airlines website into a world-class airline platform that fixes all identified weaknesses — adding booking progress indicators, a rebuilt mobile booking experience, specific error handling, destination photography and discovery, prominent live support, and a visual identity that matches the airline's standing as Uganda's national carrier.

**Output:** A single-file `index.html` (HTML + CSS + vanilla JS, no build step required).

---

## 2. Weaknesses to Fix

| # | Weakness | Solution |
|---|----------|----------|
| W1 | No booking progress indicator | Step indicator throughout the booking flow (Step 1 of 4, etc.) |
| W2 | Poor mobile booking experience | Fully responsive booking form, large tap targets, mobile-first layout |
| W3 | Generic, uninformative error messages | Specific inline validation messages on every field |
| W4 | Visual design doesn't reflect a national carrier | Rich photography, destination cards, premium airline aesthetic |
| W5 | No destination discovery / inspiration section | Destination cards with photography, route highlights |
| W6 | Contact info and live support not prominent | Persistent header support link + WhatsApp floating button |

---

## 3. Aesthetic Direction

### 3.1 Theme
**National Pride · Premium African Aviation**
Think Ethiopian Airlines meets RwandAir — authoritative, warm, aspirational. Deep crane-grey and gold (Uganda's crane colours), rich destination photography, clean modern layout. Not sterile. Not generic. This is Uganda's airline.

### 3.2 Color Palette
```css
:root {
  --bg-primary:      #FFFFFF;
  --bg-soft:         #F8F6F1;   /* Warm cream — alternate sections */
  --bg-dark:         #0D1B2A;   /* Deep navy — footer, nav overlay */
  --accent-navy:     #1B3A5C;   /* Primary brand — nav, headings, CTAs */
  --accent-gold:     #C9933A;   /* Uganda crane gold — highlights, accents */
  --accent-gold-l:   #E8B86D;   /* Lighter gold — hover, badges */
  --accent-red:      #C0392B;   /* Error states */
  --accent-green:    #27AE60;   /* Success, confirmed states */
  --text-primary:    #1A1A1A;
  --text-muted:      #6B7280;
  --text-light:      #F8F6F1;
  --border:          #E5E0D8;
  --step-active:     #1B3A5C;
  --step-done:       #27AE60;
  --step-pending:    #D1D5DB;
}
```

### 3.3 Typography
```html
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@400;600;700&family=Inter:wght@300;400;500;600&display=swap" rel="stylesheet">
```
- **Display / Hero:** `Cormorant Garamond` — elegant, authoritative serif for headlines
- **Body / UI / Forms:** `Inter` — clean, highly legible, works perfectly at small sizes
- **Prices / Step numbers:** `Inter`, bold, `--accent-gold`

### 3.4 Visual Details
- **Hero:** Full-viewport dark gradient overlay over warm destination photography (CSS gradient fallback)
- **Destination cards:** 16:9 ratio, rounded corners `12px`, rich gradient overlays, destination name bottom-left
- **CTAs:** `--accent-gold` background, dark text, `border-radius: 6px`, hover darkens to `--accent-gold-l`
- **Step indicator:** Horizontal numbered circles connected by a line — green filled (done), navy filled (active), grey outline (pending)
- **Form inputs:** Large height `52px`, clear labels above (not placeholder-only), strong focus ring in `--accent-gold`
- **Error states:** Red border + red icon + specific message below the field — never a generic top-level alert
- **Success states:** Green border + checkmark icon

---

## 4. Page Structure & Sections

Single scrolling `index.html` with a **sticky navigation bar**.

---

### 4.0 `<nav>` — Sticky Navigation

- **Left:** Uganda Airlines crane logo (CSS — gold circle with "UR" text) + **Uganda Airlines** in Cormorant Garamond
- **Center:** `Home | Destinations | Manage Booking | Check-In | More`
- **Right:**
  - `📞 Support` text link (opens contact section)
  - `My Booking` outlined button
  - `Book Now` gold filled button
- **Mobile:** Hamburger collapse
- Starts transparent over hero; gains `--bg-dark` background with gold bottom border on scroll

---

### 4.1 `#hero` — Hero Section

Full viewport (`100vh`).

**Background:** `linear-gradient(160deg, #0D1B2A 0%, #1B3A5C 50%, #2C5F8A 100%)` — deep navy suggesting sky and altitude.

**Content (left-aligned, 55% width desktop):**
- Small tag: `🇺🇬  Uganda's National Airline · Est. 2019`
- Headline (Cormorant Garamond 700, large, white): `"Africa and Beyond, From the Pearl of the World"`
- Sub-line (Inter, muted light): `"Book direct to Nairobi, Dar es Salaam, Juba, Kinshasa and more — with Africa's youngest fleet."`

**Booking widget (below headline, full width of left column):**
```
[○ One Way]  [● Return]  [○ Multi-City]

From:  [ Entebbe (EBB) ▾ ]     To:  [ Select destination ▾ ]
Depart: [ dd/mm/yyyy 📅 ]      Return: [ dd/mm/yyyy 📅 ]
Passengers: [ 1 Adult ▾ ]      Class: [ Economy ▾ ]

[ Search Flights → ]  (gold button, full width)
```
All fields large (`52px` height), clearly labeled. Dropdowns use custom styled `<select>` elements. Date fields use `<input type="date">` with custom CSS.

**Right side (40% width, desktop only):** A stacked card showing a destination highlight:
```
┌────────────────────────────────┐
│  [Destination gradient card]   │
│  Nairobi, Kenya                │
│  From USD 189  ·  1h 20m       │
│  [ View Flights → ]            │
└────────────────────────────────┘
```
Auto-cycles through 3 destinations every 4 seconds (CSS animation).

**Below hero — Route highlight strip (dark background):**
```
EBB → NBO  ·  EBB → DAR  ·  EBB → JIA  ·  EBB → FIH  ·  EBB → MBA  ·  EBB → BJM
Nairobi     Dar es Salaam   Juba         Kinshasa      Mombasa       Bujumbura
```
Horizontal scrollable strip. Each route is a clickable pill that pre-fills the booking form.

---

### 4.2 `#booking` — Flight Booking Flow with Step Indicator

> Fixes W1 and W2. This is the most critical section.

When a user submits the hero search form, smooth-scroll to this section which shows a full booking flow with a prominent **4-step progress indicator** at the top.

**Step Indicator (always visible at top of booking section):**
```
①──────────②──────────③──────────④
Select      Passenger   Seat        Payment
Flights     Details     Selection   & Confirm
```
- Circle filled green + checkmark = completed step
- Circle filled navy = current step
- Circle outline grey = upcoming step
- Line between circles: green for done, grey for upcoming

**Step 1 — Select Flight:**
Flight results display as cards:
```
┌─────────────────────────────────────────────────────────────┐
│  UGANDA AIRLINES                                UR 101      │
│  🛫 07:30  EBB          ───────  1h 20m  ───────  08:50 NBO │
│  Entebbe                Non-stop               Nairobi      │
│                                                             │
│  Economy: USD 189    Business: USD 420    [ Select → ]      │
└─────────────────────────────────────────────────────────────┘
```
Show 3 sample flight cards. `Select` advances to Step 2 with animation.

**Sample flights (use these):**
```javascript
const sampleFlights = [
  { flight: "UR 101", from: "EBB", to: "NBO", dep: "07:30", arr: "08:50", duration: "1h 20m", economy: 189, business: 420 },
  { flight: "UR 201", from: "EBB", to: "DAR", dep: "10:15", arr: "12:00", duration: "1h 45m", economy: 215, business: 490 },
  { flight: "UR 305", from: "EBB", to: "FIH", dep: "14:00", arr: "17:30", duration: "3h 30m", economy: 340, business: 680 },
];
```

**Step 2 — Passenger Details:**
```
Title [ Mr ▾ ]    First Name [__________]    Last Name [__________]
Date of Birth [ dd/mm/yyyy ]    Nationality [__________]
Passport Number [__________]    Passport Expiry [ dd/mm/yyyy ]
Email [__________]              Phone [__________]
```

**CRITICAL — Inline field validation (fixes W3):**
Every field must validate on `blur` (when user leaves it). Show specific messages:
- Empty required field: `"First name is required"` (not "Please fill in this field")
- Invalid email: `"Enter a valid email address, e.g. name@email.com"`
- Expired passport: `"Passport must be valid for at least 6 months beyond your travel date"`
- Invalid date: `"Please enter a valid date in DD/MM/YYYY format"`

Red border + red `⚠` icon + message directly below the field. No page-level alerts.

**Step 3 — Seat Selection:**
Simple CSS grid of seats (6 columns: A B C | D E F). Color key:
- Navy = taken
- Gold = selected (user's current pick)
- Green = available economy
- Light gold = available business
Click to select. Show selected seat number below the map. `[ Continue → ]` button.

**Step 4 — Payment & Confirm:**
```
Order Summary:
  EBB → NBO, UR 101, 07:30 — John Doe — Seat 14C
  Economy Class                           USD 189.00
  Airport Tax                              USD 28.00
  Total                                   USD 217.00

Payment Method:
  (●) Credit / Debit Card
  ( ) Mobile Money (MTN / Airtel)
  ( ) Bank Transfer

Card details:
  Card Number [____  ____  ____  ____]
  Name on Card [__________]
  Expiry [MM/YY]    CVV [___]

[ ✓ Confirm & Pay USD 217.00 ]  (large gold button)
```
On confirm: show success screen with booking reference, passenger name, flight details, and `[ Download Boarding Pass PDF ]` button (triggers browser print dialog).

---

### 4.3 `#destinations` — Destination Discovery

> Fixes W5.

**Section heading (Cormorant Garamond):** `"Where Will You Go?"`
Sub-line: `"Direct routes from Entebbe International Airport"`

**Destination cards grid — 3 columns desktop, 2 tablet, 1 mobile:**

Each card:
```
┌──────────────────────────────────┐
│  [Dark gradient over city color] │
│                                  │
│  Destination Name                │  ← bottom overlay, Cormorant Garamond
│  Country  ·  From USD XXX        │  ← Inter, muted light
└──────────────────────────────────┘
```
Hover: card scales `1.03`, gold border appears.

**Destination data (use exactly — all real Uganda Airlines routes):**

| City | Country | Color Gradient | From (USD) | Flight Time |
|------|---------|----------------|------------|-------------|
| Nairobi | Kenya | `#1a472a → #2d6a4f` | 189 | 1h 20m |
| Dar es Salaam | Tanzania | `#1d3557 → #457b9d` | 215 | 1h 45m |
| Mombasa | Kenya | `#0077b6 → #00b4d8` | 230 | 1h 55m |
| Juba | South Sudan | `#7b2d00 → #c46200` | 260 | 1h 30m |
| Bujumbura | Burundi | `#2d6a4f → #52b788` | 245 | 1h 40m |
| Kinshasa | DR Congo | `#4a1942 → #7b2d8b` | 340 | 3h 30m |
| Mogadishu | Somalia | `#7b3f00 → #c47c00` | 290 | 2h 10m |
| Zanzibar | Tanzania | `#006494 → #00b4d8` | 225 | 1h 50m |

Clicking a card pre-fills the booking form origin (EBB) and destination, then smooth-scrolls to #booking.

---

### 4.4 `#checkin` — Online Check-In

Clean, minimal section:

**Heading:** `"Check In Online — Up to 48 Hours Before Your Flight"`

**Form:**
```
Booking Reference  [__________]    (e.g. AB1234)
Last Name          [__________]

[ Find My Booking → ]
```

On submit (JS simulation): show a mock boarding pass card:
```
┌─────────────────────────────────────────────┐
│  UGANDA AIRLINES          BOARDING PASS      │
│  John Doe                 UR 101             │
│  EBB → NBO                07:30             │
│  Seat 14C · Economy · Gate 5                │
│  [||||||||||||||||||||  Barcode  ||||||||]   │
└─────────────────────────────────────────────┘
[ Download / Print ]
```

Inline validation: if reference field is empty on submit → `"Please enter your booking reference (e.g. AB1234)"` — not a generic alert.

---

### 4.5 `#manage` — Manage Booking

Three option cards in a row:

```
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│  ✏️ Change       │  │  ❌ Cancel       │  │  🧳 Add Bags     │
│  Flight          │  │  Booking         │  │  or Extras       │
│                  │  │                  │  │                  │
│  Change your     │  │  Cancel and      │  │  Add checked     │
│  travel dates    │  │  request a       │  │  baggage, meals  │
│  or destination  │  │  refund          │  │  or seat upgrade │
│                  │  │                  │  │                  │
│  [ Manage → ]    │  │  [ Manage → ]    │  │  [ Manage → ]    │
└──────────────────┘  └──────────────────┘  └──────────────────┘
```

Each `Manage →` button opens a small modal with: Booking Reference field + Last Name field + action-specific message, then shows: `"✓ Request received. We will contact you at your registered email within 24 hours."`

---

### 4.6 `#support` — Contact & Support

> Fixes W6.

**Section heading:** `"We're Here When You Need Us"`

**Two columns:**

**Left — Quick contact cards (3 items):**
```
📞 Call Us
+256 200 406400
Mon–Fri 8AM–8PM · Sat–Sun 9AM–5PM (EAT)
[ Call Now → ]

✉️ Email Us
reservations@ugandairlines.com
Response within 24 hours
[ Send Email → ]

💬 WhatsApp
Fast responses for urgent queries
[ Chat on WhatsApp → ]
```

**Right — FAQ accordion (5 questions, JS toggle):**
```
▼ How early can I check in online?
   Online check-in opens 48 hours before departure and
   closes 2 hours before. Select your seat and download
   your boarding pass from your device.

▼ What is the baggage allowance?
   Economy: 23kg checked + 7kg cabin.
   Business: 32kg checked + 7kg cabin.

▼ Can I change my flight after booking?
   Yes. Changes can be made up to 24 hours before departure
   via Manage Booking or by calling our reservations line.

▼ Does Uganda Airlines fly to London?
   Yes — service to London Gatwick launched in May 2025.
   Book direct on this website.

▼ What payment methods are accepted?
   Visa, Mastercard, and mobile money (MTN, Airtel).
```

---

### 4.7 `#footer` — Footer

**Pre-footer CTA strip (navy background, gold text):**
`"Uganda's Youngest Fleet. Africa's Fastest-Growing Airline."` + `[ Book Your Flight → ]`

**Footer — 4 columns, `--bg-dark`:**
1. **Brand** — Logo, tagline, social icons (Facebook, Instagram, Twitter, LinkedIn)
2. **Fly** — Book a Flight, Check In, Manage Booking, Flight Status, Timetable
3. **Information** — Destinations, Baggage Policy, Onboard Experience, Special Assistance, Customer Service Plan
4. **Contact** — Phone, Email, HQ address (Entebbe International Airport), Interline partners (Emirates, Qatar Airways)

**Copyright:** `© 2025 Uganda National Airlines Company Limited. All Rights Reserved.`

---

### 4.8 WhatsApp Floating Button

Fixed, bottom-right, always visible:
```css
position: fixed; bottom: 24px; right: 24px; z-index: 9999;
```
Green circle, WhatsApp SVG, pulse animation.
Links to: `https://wa.me/256200406400?text=Hello%2C%20I%20need%20help%20with%20my%20Uganda%20Airlines%20booking`

---

## 5. Responsive Breakpoints

| Breakpoint | Layout |
|------------|--------|
| `< 480px` | Single column, full-width booking form, step labels hidden (numbers only), hamburger nav |
| `480–768px` | 2-col destination grid, stacked booking form |
| `> 768px` | Full desktop layout, 3-col destination grid, side-by-side hero |

**Mobile-specific booking form rules (fixes W2):**
- All inputs minimum `52px` height
- `font-size: 16px` on inputs (prevents iOS zoom)
- Date pickers: native `<input type="date">` — no custom JS pickers
- Dropdowns: native `<select>` styled with CSS — no custom dropdowns that break on mobile
- Step indicator collapses to `Step 2 of 4` text on mobile (numbers only, no labels)

---

## 6. JavaScript Requirements

Vanilla JS only.

| Feature | JS |
|---------|----|
| Booking step navigator | Show/hide step panels, update step indicator state |
| Inline form validation | `blur` event on each input → specific error message |
| Destination card click | Pre-fill booking form, smooth scroll to #hero |
| Route strip click | Same as destination card |
| Flight selection | Mark selected flight, advance to step 2 |
| Seat map click | Toggle seat selected state, update seat display |
| Check-in form submit | Validate, show mock boarding pass |
| Manage booking modals | Open/close, validate, show success |
| FAQ accordion | Toggle `max-height` on each item |
| Nav scroll behaviour | Add dark bg + shadow after `scrollY > 80` |
| Hero destination cycle | CSS animation cycling 3 destination cards |
| Smooth scroll | All `a[href^="#"]` |
| Hamburger menu | Toggle mobile nav |
| WhatsApp float | Always present, no JS needed |

---

## 7. Code Quality Rules

1. No external CSS frameworks — all CSS from scratch using the design system in Section 3
2. No external JS libraries — vanilla JS only
3. All colors via `var(--accent-gold)` etc. — never hardcode hex in CSS rules
4. Every input has a `<label>` above it — never placeholder-only labels
5. Semantic HTML: `<nav>`, `<main>`, `<section>`, `<form>`, `<footer>`
6. Destination cards: CSS gradient backgrounds with text overlays — no external images required
7. Zero network requests except Google Fonts

---

## 8. File Output

Single file: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- meta, title, viewport, Google Fonts -->
  <style>
    /* ALL CSS — variables → reset → nav → hero → booking-steps →
       destinations → checkin → manage → support → footer → floating-btn */
  </style>
</head>
<body>
  <!-- nav, hero (with booking widget), route strip,
       booking flow (4 steps + step indicator), destinations,
       check-in, manage booking, support + FAQ, footer, WhatsApp -->
  <script>
    // ALL JS — step navigator, form validation, destination prefill,
    // seat map, check-in mock, FAQ accordion, nav scroll, hamburger
  </script>
</body>
</html>
```

---

## 9. Agent Instructions Summary

1. Read this entire document before writing any code.
2. Fix **all 6 weaknesses** — every section listed must be present.
3. Use **exactly** the color palette and fonts (Cormorant Garamond + Inter) from Section 3.
4. The **4-step booking indicator** must be visible and functional throughout the booking flow.
5. **Every form field** must have specific inline validation on `blur` — no generic alerts anywhere.
6. The **destination grid** must use all 8 real Uganda Airlines routes with correct data.
7. The **seat map** must be a real interactive CSS grid — not a placeholder.
8. Mobile inputs must use `font-size: 16px` and `52px` min-height to prevent zoom and ensure usability.
9. No CSS framework, no JS library — one clean, commented `index.html`.
