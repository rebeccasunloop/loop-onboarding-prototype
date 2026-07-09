# Onboarding Prototype — Demo Cheatsheet

Magic inputs and special-cased values for demoing edge cases. Written against
`Iterations/Onboarding_Redesign_V2.3.html`; earlier iterations share most of these.

## Sign in / sign up

| Input | Result |
|---|---|
| Password under 12 chars | "The email or password you entered is incorrect" — 5 misses in a row → "Too many attempts" lockout with a reset link |
| Password 12+ chars (anything) | Accepted, proceeds to 2FA |
| 6-digit code `123456` | The only valid code **everywhere**: sign-in 2FA, SMS verification, authenticator/MFA setup. Anything else → error state |
| Sign in with the same email as your last session | Resumes where you left off (application → wait room → approved → live, depending on state) |
| Sign in with a new email | Wipes state, starts a fresh application at "Find your business" |
| "Continue with Google" | Instant fresh session as `you@gmail.com` |
| Sign-up phone | Any 7–15 digits |
| Forgot password | Any email accepted; after ~2.6 s it auto-jumps to the reset screen (new password needs 12+ chars) |

## Find your business (registry search)

Type **`northwind`** to demo the ranked type-ahead. A digits-only query searches
by tax ID (e.g. `123456782`).

### Records with useful gaps

| Record | Demo |
|---|---|
| Northwind Robotics Inc. | No description on file → red gap markers after prefill; 3 suggested directors (Daniel Okafor, Sarah Chen, Priya Raman) |
| Northwind Robotics Group Ltd. | No website on file → website field starts empty |
| Northwind Robotic Solutions ULC | No phone on file; directors Marcus Liu, Anita Deol |
| Maple & Crumb Bakery Ltd. (Halifax) | Directors include Rebecca Sun; no description |

### Soft-decline records

Confirming any of these ends the flow at the rejection screen:

- **Dépanneur du Plateau Enr.** — Quebec sole proprietorship
- **Emerald Leaf Cannabis Co. Inc.** — restricted industry (cannabis)
- **Riverbend Gaming & Casino Ltd.** — restricted industry (gambling)
- **Maple Chain Crypto Exchange Inc.** — restricted industry (crypto)

### Other paths

- **"I can't find my business" → upload any file** → simulated extraction always
  returns the Northwind Robotics Inc. record.
- **Manual path** (skip the search): answering the industry question with
  cannabis/gambling/crypto, or **Yes** to the Quebec-sole-proprietorship
  question → soft decline.

## Application fields

- **CRA business number** — must be 9 digits, start with 1/7/8, and pass Luhn.
  Valid: `123456782` (or any registry tax ID above). Easy invalid demo:
  `123456789` → inline warning.
- **Contacts** — on a record with directors, typing a first + last name matching
  a suggested director links your entry to that translucent registry card.
- **SIN** — any non-empty value (no checksum).
- **Document screening triggers** — entity type *Sole proprietorship* or
  *Partnership* → "Proof of business operations" requested; related business =
  *Yes* → "Ownership structure document". Corporation + No → nothing requested.
- **Upload filename check** — the filename must contain one of: `incorp`,
  `certificate`, `articles`, `registr`, `invoice`, `contract`, `lease`,
  `statement`, `licence`/`license`. Anything else (e.g. `IMG_2041.png`) gets the
  amber "doesn't look like an accepted document type" warning with a
  "continue anyway" override.

## Demo harness (floating pill, bottom center)

All prototype controls live in the **global demo harness** — the dark
"Prototype / Acting as" pill fixed to the bottom of every screen. Nothing
demo-related remains in the main UI.

- **Acting as** — segmented switcher between **User A (CEO)** (the signed-in
  identity) and **User B (CFO)** ("Jordan Lee"). Whoever is active is credited
  for typing and submissions; simulated background edits always land under the
  *other* user's name.
- **Demo tools** expands a panel whose sections follow the current screen:
  - **Simulate teammate** (application, pre-submit) — **Simulate background
    edit by teammate** (instantly changes the business address + phone under
    the other user's name) and an **Auto background edits** toggle (an edit
    every ~6 s; off by default). Fields you're focused in never change under
    you — held-back edits apply on blur. Editing after the Review page is
    open arms the **submit intercept**: Submit is blocked by a diff modal
    (Keep editing → blue row highlights; Confirm & submit → wait room).
  - **Flow shortcuts** (application) — Skip to wait room, invitee-experience
    preview.
  - **Wait room** (post-submit) — Simulate approval / document request /
    correction request / clear, the in-review state switcher
    (Normal / Delayed / Material requested), and per-person
    "mark verified" for pending identity checks.
  - **Cheatsheet** — the magic values from this file, always visible.
- Every filled field shows a low-profile **"🔄 Last edit: [name] • [time]"**
  stamp underneath, cross-fading when a background update changes the editor.

Other notes:

- Sign-in 2FA method list is randomized per attempt (SMS always present) —
  retry sign-in to see different method sets.

## Notes

- The chat widget is canned: quick-reply buttons have answers; free text always
  gets the same "team will get back to you" reply. No magic strings.
- State persists in `localStorage` (`loop-onboarding-app-v9`). To hard-reset a
  demo, sign in with a different email or clear site data.
