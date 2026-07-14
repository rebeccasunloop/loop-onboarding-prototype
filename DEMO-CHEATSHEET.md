# Onboarding Prototype — Demo Cheatsheet

Magic inputs and special-cased values for demoing edge cases. Written against
`Iterations/Onboarding_Redesign_V2.5.html`; earlier iterations share most of these.

## Sign in / sign up

| Input | Result |
|---|---|
| Password under 12 chars | "The email or password you entered is incorrect" — 5 misses in a row → "Too many attempts" lockout with a reset link |
| Password 12+ chars (anything) | Accepted, signs you straight in — there is no MFA step at sign-in |
| 6-digit code `123456` | The only valid code everywhere it appears: sign-up SMS verification and authenticator/MFA setup. Anything else → error state |
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

## Demo harness (hidden top-left menu)

All prototype controls live in the **global demo harness** — hidden by default
behind a small translucent sliders icon in the top-left corner of every screen
(it brightens on hover). Clicking it drops the demo-tools menu down from the
corner, so it never covers the content in the middle of the screen. Nothing
demo-related remains in the main UI.

- The menu's sections follow the current screen:
  - **Collaboration** (application, pre-submit). Simulates a teammate editing the
    same live application. Buttons, in order:
    - **Alex joins the application / Alex leaves**: brings the Alex Rivera persona
      online or offline. Their initials avatar appears (or fades) by the sidebar
      pill and on the welcome block.
    - **Priya joins too / Priya leaves**: a second persona, for the overlapping
      avatars look. Priya never edits; she is presence only.
    - **Alex edits answers on other pages**: applies a few edits to pages other
      than the one you are on. Blue count badges appear on those steps; visiting
      shows the field highlights and notes.
    - **Alex edits this page while you watch**: applies one or two edits to
      non focused fields on the current page, live, with a brief pulse.
    - **Alex edits the same field as you**: arms a write conflict. Click a field,
      type, and leave it; Alex will have changed it too, so the conflict card
      appears on save.
    - **Alex enters the same answer as you**: arms the same field case where both
      of you land on the same value, so there is nothing to resolve.
    - **Alex changes answers you already reviewed** (review page only): changes
      two reviewed answers invisibly. Nothing shows until you click Submit, which
      triggers the one look intercept.
    - **Simulate coming back after a week**: dates four edits six days ago and
      shows the "while you were away" banner.
    - **Alex adds a document / Alex updates a contact**: entity level changes that
      surface as notes and badges.
  - **Flow shortcuts** (application) — Skip to wait room.
  - **Wait room** (post-submit) — Simulate approval / document request /
    correction request / clear, the in-review state switcher
    (Normal / Delayed / Material requested), a per-person invitee-IDV
    preview, and per-person "mark verified" for pending identity checks.
  - **Cheatsheet** — the magic values from this file, always visible.

## Collaborators (V3.2 model)

- **Full-access concurrent editing.** Inviting a teammate (by email, from the
  welcome block or the sidebar pill) gives them full view and edit access to the
  whole application. There is no page ownership, no locking, no roles, and no
  scoped visibility. Only the owner submits.
- **Presence.** Invited teammates who are online show an initials avatar by the
  sidebar pill and on the welcome block (hover for the full name). The avatar
  fades and the row closes up when they go offline or are removed.
- **Three concurrency states, always measured against what you last saw** (never
  elapsed time). A run with no collaborator activity shows none of this:
  1. **Write conflict** (you and a teammate changed the same field at the same
     moment). Shown inline under the field on save, with both values written out
     so you can pick one.
  2. **Changed since you looked** (a field changed since you last viewed its
     page). Shown three ways: a highlight ring plus a note on the field (who,
     what, and the previous value), a blue count badge on the step, and a "while
     you were away" banner on return.
  3. **Changed before submit** (answers changed since you last reviewed them).
     Shown on the review page as highlighted rows, and intercepted once when you
     click Submit.
- **Blue vs grey.** Blue always means "a teammate changed this, informational."
  Grey (the warn dot and "N incomplete" chip) means "you still have to fill this
  in." They are different colors on purpose and can appear at the same time.
- **Transfer ownership** is unchanged: still the quiet link, now living in the
  People with access panel (Settings), pending until the other person accepts.

## Approval (V2.5)

- Approval lands on a decision screen: **Review and sign agreements** (primary)
  or **Finish optional setup first** (returns to the hub, which shows a green
  "approved — everything below is optional" banner with a "Go to agreements"
  button).

## Notes

- The chat widget is canned: quick-reply buttons have answers; free text always
  gets the same "team will get back to you" reply. No magic strings.
- State persists in `localStorage` (`loop-onboarding-app-v10`). To hard-reset a
  demo, sign in with a different email or clear site data.
