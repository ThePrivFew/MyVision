# MyVision Share Meals — Claude Code Handover
## For ongoing editing of `MyVision_ShareMeals_Prototype_FINAL.html`

---

## HOW TO USE THIS HANDOVER

This document tells Claude Code everything it needs to edit the prototype correctly without rebuilding from scratch. The prototype file is the source of truth. **Never rebuild it from description. Always read the file first, then make targeted edits.**

### First instruction to give Claude Code on every session:
> "Read `MyVision_ShareMeals_Prototype_FINAL.html` fully before making any changes. Edit the existing file. Do not rebuild it. The design decisions in this handover are final unless Dave explicitly overrides them."

---

## WHAT THE FILE IS

A single self-contained HTML prototype (`MyVision_ShareMeals_Prototype_FINAL.html`) that simulates four user flows for the MyVision Share Meals feature. It runs in any browser with no server or dependencies (except Google Fonts via CDN).

- **41KB, ~625 lines**
- All CSS in `<style>` block at top
- All JS in `<script>` block at bottom
- All screen content defined as template literal constants in the JS, then assembled into a `flows` array
- A lightweight engine renders screens into a phone frame component with slide transitions

---

## FILE ARCHITECTURE

### CSS (top of file)
CSS custom properties (`:root`) define the design tokens. Key variables:
```
--red: #C0152B       (primary action colour)
--navy: #1B2A5E      (nav bars, text, avatars)
--navy-dark: #0E1A3D (page header)
--blue-active: #4FC3F7 (active nav item)
--green-bg: #3DAA5C  (success banner background)
--border: #E8E8E8
--border-light: #F0F0F0
--muted: #8A93A2
```

CSS class naming convention: short utility names (`.sb` = status bar, `.nb` = nav bar, `.sc` = scroll content, `.bnav` = bottom nav, `.bni` = bottom nav item, `.fab` = floating action button, `.mrow` = meal row, etc.)

### JS constants (before the flows array)
Reusable HTML chunks defined as `const`:
- `IC` — SVG icon paths (star, activity, bowl, person, plus)
- `BN(active)` — bottom nav, pass active tab name: `'meals'`, `'profile'`, `''`
- `SB(time)` — status bar
- `NB(back, title, action)` — nav bar
- `TABS_ALL` — tab row with ALL active
- `TABS_SHARED` — tab row with SHARED active
- `TABS_SHARED_DOT` — tab row with red dot on SHARED tab (unread indicator)
- `SRCH_MEALS` — search bar
- `MEALS_LIST` — full meals list content
- `MEAL_DETAIL_BODY` — full meal detail (macros, servings, ingredients, button stack)
- `RECIPIENT_PICKER_EMPTY` — picker before selection
- `RECIPIENT_PICKER_SEL` — picker with 2 selected (AS + RC)
- `NOTIFICATIONS_SCREEN` — notifications list
- `SHARED_MEAL_DETAIL` — shared meal view with attribution banner
- `LOG_FOOD_CONFIRMED` — Log Food screen with green banner and Breakfast confirmed

### Flows array
```js
const flows = [ { tag, title, desc, screens: [ { html }, ... ] }, ... ]
```
Each screen is `{ html: string }`. The `html` is built by combining constants:
```js
SB('10:09') + NB('✕','Share meal','') + RECIPIENT_PICKER_EMPTY + BN('meals')
```

### Engine (bottom of JS)
`buildNav()`, `updateMeta()`, `renderDots()`, `renderScreen(idx, dir)`, `goTo(i)`, `nextStep()`, `prevStep()`, `switchFlow(idx, btn)` — standard click-through logic. Do not touch unless the engine itself needs changing.

---

## THE FOUR FLOWS

### Flow 1 — Privacy & Consent (4 screens)
1. My Food & Meals list — entry point, no interruption
2. Meal detail (Blueberry Smoothie) — full real-app layout, 4 buttons at bottom
3. First-share consent card — one-time only, checkbox, Confirm / Not now
4. Profile screen — Image Management (existing radio buttons) + Meal Sharing (new checkbox)

### Flow 2 — Share a Meal / Sender (4 screens)
1. Meal detail — same as F1 S2
2. Recipient picker — empty state, Recent bubbles, All Studio Members list
3. Recipient picker — 2 selected (AS + RC with checkmarks), "Share with 2 people →"
4. Sent confirmation — green banner at very top of screen, meal detail below

### Flow 3 — Receiving a Shared Meal (4 screens)
1. Lockscreen push notification
2. Notifications list — TODAY/EARLIER groups, pink bg on shared rows
3. Shared meal detail — "Shared by David Drury" attribution banner, Add to plan primary
4. Log Food confirmed — green banner top, Breakfast slot shows meal added

### Flow 4 — Shared With Me (4 screens)
1. My Food & Meals — SHARED tab has red notification dot
2. Shared With Me screen — SHARED tab active, 2 meals with sender avatar + name + time
3. Shared meal detail — same as F3 S3
4. Log Food confirmed — same as F3 S4

---

## DESIGN SYSTEM — MATCH THE REAL MYVISION APP

### Screens
- **White background** for list/detail screens
- **#F2F2F7 grey background** for meal detail content area (between nav and bottom nav)
- **No navy nav bars on app screens** — nav bar is white with navy text

### Status bar
White bg, dark text, `▌▌▌ WiFi ▮` icons. Time varies per screen.

### Nav bar
- Back: `←` or `←  [Screen name]` in navy, font-size 20px
- Title: bold navy, 17px, centred
- Action: "Edit", "(?)", or "•••" in navy/blue

### Tab row
- Scrollable horizontal (`overflow-x:auto`, `scrollbar-width:none`)
- ALL / FOODS / MEALS / RECIPES & SNACKS / SHARED
- Active tab: black bold text, red underline (2.5px)
- Inactive: muted grey

### Meal list rows
- Large bold meal name (15px, font-weight 700)
- Light grey divider between rows
- Red circle + button right (30px diameter)
- Padding: 16px all sides

### Buttons
- **Primary red filled pill** (`btn-p`): red bg, white text, 26px radius
- **Outlined red pill** (`btn-o`): white bg, red border and text, 26px radius — used for Share this meal and Delete saved meal
- **Ghost text** (`btn-g`): transparent, muted text — used for Dismiss

### Bottom nav
- Navy background
- SVG outline icons (star, activity pulse, bowl with spoon, person silhouette)
- Active item: `--blue-active` (#4FC3F7) icon and label
- Inactive: rgba(255,255,255,0.45)
- FAB: red circle 46px, centred, rises above nav bar

### Green success banner
- Full width, `--green-bg` (#3DAA5C), sits **immediately below nav bar**
- "Text message. &nbsp; **OK**" — flush top, before any content
- Used for: "Meal shared successfully." and "Added to your meal plan."

### Notifications
- TODAY / EARLIER group labels in muted uppercase
- Shared meal rows: pink background `#FFF0F1`, 🥗 icon in `#FEE2E2` circle
- System rows: white background, ⏰ or 👤 icon in `#F3F4F6` circle
- No REMINDERS/NEWS tabs on this screen
- No Delete all button

### Shared meal attribution banner
- Pink `#FFF0F1` background with `#FECACA` bottom border
- Sender avatar (navy circle, initials), sender name (red bold), timestamp below

---

## DECISIONS THAT ARE FINAL — DO NOT CHANGE WITHOUT DAVE'S EXPLICIT INSTRUCTION

1. **Scope**: My Foods & Meals only. No Vision recipes, meal plans, tags, method editing, FatSecret distinction.
2. **Privacy consent**: one-time inline card at first share. Profile screen shows existing Image Management + new Meal Sharing checkbox. No separate Privacy screen. No invented toggles (Activity visibility, Progress sharing were removed — they don't exist in the real app).
3. **Recipient picker**: name only. No email, no studio name, no trainer name. Studio is implicit from account. Recent = last 3 people, last 9 weeks.
4. **Share this meal button**: outlined red (secondary). Differentiated from Add to Meal Plan and Modify Saved Meal (both filled red primary).
5. **Green success banner**: always at top of screen immediately below nav bar, never embedded in button stack.
6. **Notifications**: only real notification types — shared meal (🥗, pink), system reminder (⏰, grey), trainer activity (👤, grey). No REMINDERS/NEWS tabs. No invented content.
7. **Log Food after Add to Plan**: shows real Log Food screen with calendar and meal slots — not a custom "Meal Plan" screen.

---

## OPEN QUESTIONS FOR ANDREW (placeholder in picker)

- What is the max shares per day / max recipients per share? (shown as "Share limit: [TBC with Andrew]")
- What is the sharing scope — same studio only, or cross-network?
- How long do received meals persist in the Shared tab?
- How to disambiguate duplicate names if trainer name is not shown?

## OPEN QUESTIONS FOR IVAN (dev handover items)

- Deep link from push notification requires a URL scheme — flag early, non-trivial
- First-share consent state stored per user, fires once only
- Shared tab badge clears when user opens the tab
- Copy vs reference decision: recommend copy for v1 (simpler, no deletion edge cases)

---

## HOW TO MAKE COMMON EDITS

### Change a screen's content
Find the screen in the `flows` array (e.g. `flows[1].screens[2]`) and edit its `html` string. If the content is a reusable constant (e.g. `RECIPIENT_PICKER_SEL`), edit the constant definition near the top of the JS block.

### Add a screen to a flow
Add a new `{ html: ... }` object to the relevant `screens` array. The dots and step counter update automatically.

### Change a button label
Search for the exact text string (e.g. `"Share with 2 people →"`) and replace.

### Change a colour
Edit the CSS custom property in `:root` or the specific class. Always match the real app — primary action colour is `#C0152B`, never approximate.

### Add a new flow
Push a new object onto the `flows` array with `tag`, `title`, `desc`, and `screens`. The flow nav builds itself.

### Debug a blank phone / script error
The most common cause is an unclosed template literal or mismatched backtick in a screen's `html` string. Search for the last working screen and check the one after it.

---

## CLAUDE CODE SETUP INSTRUCTIONS

1. Install Claude Code: `npm install -g @anthropic-ai/claude-code`
2. Open terminal in the folder containing `MyVision_ShareMeals_Prototype_FINAL.html`
3. Run `claude`
4. First message every session: *"Read MyVision_ShareMeals_Prototype_FINAL.html. Edit it in place. Do not rebuild. Here is what I need changed: [your change]"*

The file is self-contained. No build step, no dependencies to install. Open in any browser to preview.

---

*Handover prepared by Dave Drury / TPF Pty Ltd, June 2026. MyVision is owned by Vision Personal Training. Prototype built for developer handover to Ivan.*
