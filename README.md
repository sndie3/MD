# MD
Masterclass Documentation

# MASTERCLASS — User Flow Guide

A walkthrough of every feature in the system, organized from the most basic entry points to advanced management tasks.

The system has two frontends:

| Frontend | URL entry point | Who uses it |
|---|---|---|
| **MasterClass** (Agent App) | `/login` | Agents (all levels) and players |
| **Backoffice** (Admin Dashboard) | `/features` | Masterclass admin / operators |

Both share the same FastAPI backend and SQLite database.

---

## SYSTEM COMPLETION: ~88%

| Component | Working | Partial | Not Implemented | Total | Score |
|---|---|---|---|---|---|
| **Backend API** | 44 | 1 | 4 | 49 | 90% |
| **Backoffice Frontend** | 19 | 1 | 0 | 20 | 97% |
| **MasterClass Frontend** | 18 | 2 | 5 | 25 | 76% |
| **OVERALL** | **81** | **4** | **9** | **94** | **~88%** |

### What's Working vs Not Implemented (Summary)

**✅ Working (81 features):** Login, Dashboard, Agent Registration, Agent Approval, Account Status, Agent Tree, Level Creation, Player Registration, Player Lists, Credit Request, Credit Send, Commission (full suite), Withdrawals, Logs, Statistics (partial), Earnings, Profile, Selfie with ID, View ID, Share Referral, Link Player Account, Theme, Chat Settings, Chat Wallpaper, Change Chat Name, App Icon, Profile Security, Settings navigation.

**⚠️ Partially Working (4 features):** Statistics (5 of 12 stats hardcoded to 0), Notifications (UI toggles work but state not persisted), Deactivate Account (clears localStorage but no backend API call).

**❌ Not Implemented (9 features):** Reset Password, Change Password, Promo (static buttons with no action), Support (hardcoded dummy data, no API), Customer Support (local chat only, no backend), Password Reset/OTP API, Support/Chat API, Notification Settings API, Account Deactivation API.

---

## PART 1 — MASTERCLASS (Agent Frontend)

### 1.1 Login & Authentication — ✅ WORKING

**Page:** `/login`
**What it does:** Agents sign in with their phone number and password. A checkbox confirms agreement to Privacy Policy and Terms of Use. On success, the user is redirected to the Dashboard.

**Example:**
> An agent opens the app, enters `09123456789` and `MyPass123`, ticks the agreement box, and taps **Login**. They land on the Dashboard.

**Related page:** `/reset` — ❌ NOT IMPLEMENTED
**What it does:** Allows an agent to reset their password if they forgot it. Currently shows "Password reset is not connected yet." The form collects mobile number, new password, confirm password, and OTP, but no backend API exists for password reset or OTP generation.

---

### 1.2 Dashboard (Home) — ✅ WORKING

**Page:** `/dashboard`
**What it does:** The main hub after login. Shows the agent's username, verification status, and a grid of menu icons that navigate to all agent-facing features.

**Menu items on the Dashboard:**

| Icon | Label | Route | Description |
|---|---|---|---|
| Add Level | Add Level | `/add-level` | Create a sub-agent (child) |
| Earnings | Earnings | `/earnings` | View earnings and request withdrawals |
| Send icon | Credit Send | `/credit-send` | Manage credit requests from child agents |
| Ribbon | Promos | `/promo` | View promotional offers |
| Register | Register | `/register` | Register a new player |
| Settings | Settings | `/settings` | Open settings menu |
| Support | Support | `/support` | Open support chat |

**Sidebar (hamburger menu):** Opens a side panel with:
- **Profile** → `/profile`
- **Notification** → `/notifications`
- **Privacy** (placeholder)
- **Settings** → `/settings`
- **Promos** → `/promo`
- **Install** (PWA install prompt, if available)
- **Sign Out** — logs out and returns to `/login`

---

### 1.3 Add Level (Create Sub-Agent) — ✅ WORKING

**Page:** `/add-level`
**What it does:** Allows an agent to register a child agent one level below themselves. The form collects first name, middle name, last name, mobile number, email, birthdate, password, and a selfie-with-ID photo (camera or file upload). A username and UID are auto-generated. The child agent's level is automatically set to the parent's level + 1.

**Example:**
> A Level 2 agent named "ROGER" opens Add Level, fills in the form for a new agent "ALICE", takes a selfie with ID, and taps **Activate**. A Level 3 agent "ALICE" is created under ROGER's downline.

---

### 1.4 Register (Register a Player) — ✅ WORKING

**Page:** `/register`
**What it does:** Allows an agent to register a player under their account. The form collects first name, middle name, last name, mobile number, birthdate, password, and a selfie-with-ID photo (camera capture). On success, a player UID is returned.

**Example:**
> Agent ROGER registers a player named "JUAN" with mobile `09187654321`, takes a selfie with ID, and taps **Activate**. Player JUAN is created and linked to ROGER.

---

### 1.5 Earnings — ✅ WORKING

**Page:** `/earnings`
**What it does:** Displays the agent's real-time earnings split into two wallets: **Specialty** and **Standard**. Each wallet shows the current balance and a withdrawal button. Tapping a wallet opens a withdrawal modal where the agent enters an amount and submits a withdrawal request. A logs table appears below (currently empty/placeholder).

**Example:**
> Agent ROGER sees Specialty = 5,000 and Standard = 12,000. He taps the Standard wallet, enters 3,000, and taps **Submit Withdrawal**. A withdrawal request is sent to the backoffice for approval.

---

### 1.6 Credit Send — ✅ WORKING

**Page:** `/credit-send`
**What it does:** Shows a unified list of credit sends and credit requests from child agents. The agent can:
- **Approve** or **Disapprove** pending credit requests from children
- Mark requests as **Paid** or **Unpaid**
- Record a **Payment** for an agent (enter an amount and pay)
- View an **Unpaid Summary** showing each child agent's total owed and paid amounts
- View **Payment History** per agent

The page auto-refreshes every 500ms to show real-time updates.

**Example:**
> Agent ROGER sees a credit request from child agent ALICE for 1,000 Standard credits. He taps **Approve**, then later enters 500 in the payment box and taps **Proceed to Pay** to record a partial payment.

---

### 1.7 Profile — ✅ WORKING

**Page:** `/profile`
**What it does:** Displays the agent's full name, level, username, and UID. Shows editable email and contact fields. Provides navigation to:

| Menu Item | Route | Description |
|---|---|---|
| Link Player Account | `/link-player-account` | Link an existing player to the agent |
| Selfie with ID / View ID | `/selfie-with-id` or `/view-id` | Submit or view the agent's selfie-with-ID |
| Share Referral Code | `/profile/share-referral` | Share the agent's referral code |
| Theme | `/theme` | Choose a visual theme |
| Change Password | `/change-password` | Change the agent's password |

**Sub-pages:**
- **Link Player Account** (`/link-player-account`) — ✅ WORKING — Links an existing player to the agent via `POST /api/player-link/`
- **Selfie with ID** (`/selfie-with-id`) — ✅ WORKING — Camera capture, uploads via `PUT /api/auth/selfie`
- **View ID** (`/view-id`) — ✅ WORKING — Fetches and displays the agent's selfie-with-ID from backend, with retake functionality
- **Share Referral Code** (`/profile/share-referral`) — ✅ WORKING — Fetches real account data and displays referral code
- **Change Password** (`/change-password`) — ❌ NOT IMPLEMENTED — Shows "Password reset is not connected yet."

**Example:**
> Agent ALICE opens Profile, taps **Share Referral Code**, and sees her referral code `ALICE-ABC123` which she can share with prospective players.

---

### 1.8 Theme — ✅ WORKING (localStorage)

**Page:** `/theme`
**What it does:** Displays a carousel of 23 visual themes. The agent can swipe or use arrow keys to browse, tap **Activate** to apply a theme, or tap **Default** to reset. The selected theme changes the app's background, button, and card colors globally.

**Example:**
> Agent ROGER swipes to "Theme 5", previews it live, and taps **Activate**. The app's colors change immediately and persist across sessions.

---

### 1.9 Settings — ✅ WORKING

**Page:** `/settings`
**What it does:** A menu page linking to sub-settings:

| Option | Route | Description |
|---|---|---|
| Profile Security | `/profile-security` | Security settings for the agent's profile |
| Chat Settings | `/chat-settings` | Customize chat appearance and behavior |
| Deactivate Account | `/deactivate-account` | Deactivate the agent's account |
| Licenses | `/licenses` | View license information |
| System License | `/system-license` | View system license details |

#### Deactivate Account (`/deactivate-account`) — ⚠️ PARTIALLY WORKING
Clears localStorage and redirects to login, but does **not** call any backend API to actually deactivate the account. The account remains active in the database — the user can simply log back in.

#### Profile Security (`/profile-security`) — ✅ WORKING (localStorage)
Toggle switches for Session Logout, End-to-End Encryption, Location, Biometric Login, Device Identification, Profile Encryption, and Auto Update. All stored in localStorage.

#### Change Password (`/change-password`) — ❌ NOT IMPLEMENTED
Shows "Password reset is not connected yet." The form collects mobile number, new password, confirm password, and OTP, but no backend API exists for password changes or OTP verification.

#### Chat Settings (`/chat-settings`) — ✅ WORKING (localStorage)
Sub-options:
- **Change Chat Name** (`/change-chat-name`) — ✅ WORKING (localStorage) — Change the display name in chat
- **Chat Wallpaper** (`/chat-wallpaper`) — ✅ WORKING (localStorage) — Choose a wallpaper for chat backgrounds
- **App Icon** (`/app-icon`) — ✅ WORKING (localStorage) — Choose a custom app icon
- **Show 21+ Content** toggle — ✅ WORKING (localStorage) — Show/hide adult content media
- **Direct Share** toggle — ✅ WORKING (localStorage) — Show/hide share app link

---

### 1.10 Notifications — ⚠️ PARTIALLY WORKING

**Page:** `/notifications`
**What it does:** Manage notification preferences. Two sections — **Promo** and **Support** — each with three toggles: Email, SMS, and Notification (in-app). Toggling switches them on/off.

**What's missing:** Toggle state is stored in React state only — it resets on page reload. No localStorage persistence and no backend API to save preferences.

**Example:**
> Agent ROGER turns off SMS notifications for Promo but keeps Email and in-app notifications on. However, if ROGER navigates away and returns, all toggles reset to their defaults (all on).

---

### 1.11 Support — ❌ NOT IMPLEMENTED

**Page:** `/support`
**What it does:** A support chat interface with two tabs: **Affiliate** and **Players**. The agent can view support messages and type a message to send. The chat background uses the selected chat wallpaper.

**What's missing:** All messages are hardcoded dummy data ("Message ni boss..." with fake timestamps). The send button has no API call — messages are not actually sent or stored. No backend support/chat API exists. The game carousel section is commented out.

---

### 1.12 Customer Support — ❌ NOT IMPLEMENTED

**Page:** `/customer-support`
**What it does:** A dedicated customer support chat page accessible from the dashboard. Shows a chat interface with a welcome message and input field.

**What's missing:** Messages are stored in local React state only — they disappear on page reload. No backend API for chat messages. No real support agent on the receiving end. This is a UI-only prototype.

---

### 1.13 Promo — ❌ NOT IMPLEMENTED

**Page:** `/promo`
**What it does:** Lists promotional offers as buttons: Sign Up Bonus, Player Cash-In Rebate, Hourly Promo, Discount Program, Daily Raffle, Weekly Raffle, Monthly Raffle, and Event.

**What's missing:** All buttons have empty `onClick={() => {}}` handlers — nothing happens when tapped. No backend API for promos. No promo details, images, or registration flow. This is a static UI placeholder only.

---

## PART 2 — BACKOFFICE (Admin Dashboard)

### 2.1 Features (Landing Page) — ✅ WORKING

**Page:** `/features`
**What it does:** The main landing page of the backoffice. Displays all features grouped by category in a card grid. Each card links to the corresponding feature page. A navigation bar at the top shows the "MASTERCLASS AGENT DASHBOARD" title and a **FEATURES** button to return to this page.

**Feature groups:**

1. Agents
2. Levels
3. Players
4. Credits
5. Commission & Collection
6. Withdrawals
7. Logs
8. Statistics

---

### 2.2 Registration — ✅ WORKING

**Page:** `/registration`
**What it does:** Register a new agent. The form collects username, password, UID, first/middle/last name, contact, email, selfie-with-ID, level, scope (MASTERCLASS or DEFAULT), and referral code. Password must contain uppercase, lowercase, and a digit.

**Example:**
> Admin registers a new Level 1 MASTERCLASS agent named "CARLOS" with contact `09171112222`. The agent appears in the Registration List with status "HOLD" (pending).

**Backend endpoint:** `POST /api/agents/register`

---

### 2.3 Registration List — ✅ WORKING

**Page:** `/registration-list`
**What it does:** View all registered agents in a paginated table. Shows stats: count today, total registration, and breakdowns by status (approved, disapproved, pending). Filter by status (ACTIVE, HOLD, BLOCK). Each row shows UID, username, level, status, created date, created by, and referral code.

**Example:**
> Admin opens the Registration List, sees 150 total agents with 5 registered today. Filters by "HOLD" to review 3 pending accounts.

**Backend endpoint:** `GET /api/agents/registrations`

---

### 2.4 Agent Tree — ✅ WORKING

**Page:** `/agent-tree`
**What it does:** Visualize the agent hierarchy tree. Shows parent-child relationships across all levels. Useful for understanding the downline structure.

**Backend endpoint:** `GET /api/agents/all` and `GET /api/agents/{id}/children`

---

### 2.5 Account Status — ✅ WORKING

**Page:** `/account-status`
**What it does:** View and manage approved agents split into **Online** and **Offline** tables. Each row shows rank, username, contact, standard earnings, specialty earnings, and total earnings. Search by username or contact. The admin can change an agent's account status to **BLOCK**, **HOLD**, or **ACTIVE**.

**Example:**
> Admin sees agent ROGER is online with 17,000 total earnings. Admin sets ROGER to "HOLD" due to a compliance issue — ROGER is immediately blocked from logging in.

**Backend endpoints:** `GET /api/agents/account-status`, `POST /api/agents/account-status/{id}`

---

### 2.6 Account Approval — ✅ WORKING

**Page:** `/approval`
**What it does:** Review pending agent accounts and approve or disapprove them. Approved agents can log in; disapproved agents are blocked.

**Example:**
> Agent CARLOS registered and is pending. Admin reviews his selfie-with-ID, taps **Approve**. CARLOS receives "approved" status and can now log in to the MasterClass app.

**Backend endpoints:** `POST /api/agents/approve/{id}`, `POST /api/agents/disapprove/{id}`

---

### 2.7 Level Creation — ✅ WORKING

**Page:** `/level-creation`
**What it does:** Set the maximum agent level per scope. Two scopes are shown: **MASTERCLASS** and **DEFAULT**. Each scope has a single row showing the current max level. In edit mode, the admin can use `+` / `-` buttons or a number input to add/remove levels. An **ONLINE** toggle controls whether the scope is active.

**Example:**
> Admin sets MASTERCLASS max level to 7 and DEFAULT max level to 2. Agents cannot create sub-agents beyond these levels. Admin toggles DEFAULT scope to ONLINE to activate it.

**Backend endpoints:** `POST /api/levels`, `PUT /api/levels/scope/{scope}/add`, `PUT /api/levels/scope/{scope}/remove`, `PUT /api/levels/scope/{scope}/online`

---

### 2.8 Default Player — ✅ WORKING

**Page:** `/default-player`
**What it does:** View the list of players in the DEFAULT scope. Shows player details including name, UID, and linked agent.

**Backend endpoint:** `GET /api/players/default`

---

### 2.9 Non-Default Player — ✅ WORKING

**Page:** `/non-default-player`
**What it does:** View the list of players in the NON DEFAULT scope. Same layout as Default Player.

**Backend endpoint:** `GET /api/players/non-default`

---

### 2.10 Credit Request — ✅ WORKING

**Page:** `/credit-request`
**What it does:** View credit requests submitted by agents. Each request shows date, time, type (STC = Standard, SPC = Specialty), amount, requested by, category, running total, overall balance, approved by, and status (PENDING, APPROVED, DISAPPROVED).

**Example:**
> Agent ALICE requests 2,000 Standard credits. The admin sees this in the Credit Request page with status "PENDING". The parent agent (ROGER) approves it in the MasterClass Credit Send page.

**Backend endpoints:** `POST /api/credit-request/`, `GET /api/credit-request/logs`

---

### 2.11 Credit Send — ✅ WORKING

**Page:** `/credit-send`
**What it does:** View credit sends from parent agents to child agents. Shows date, time, type, amount, receiver, UID, level, payment status (PAID/UNPAID), and approval status. The admin can filter by credit type and payment status. Also shows credit send counts (total, pending, paid, unpaid) and available balances.

**Backend endpoints:** `GET /api/credit-send/`, `GET /api/credit-send/counts`, `GET /api/credit-send/balances`

---

### 2.12 Commission Settings — ✅ WORKING

**Page:** `/commission`
**What it does:** Configure commission rates for games. The admin can:
- **Create games** (name, code, type: BET/GGR/NGR, category: STANDARD/SPECIALTY)
- **Set commission mode** per game (UNCONFIGURED, STATIC, EDITABLE, DYNAMIC)
- **Edit commission templates** — set percentage per agent level per scope (MASTERCLASS or DEFAULT)
- **Set per-agent commission overrides** — assign custom percentages to specific agents
- **Configure player commission** — set the player's commission percentage per game
- **Preview commission breakdown** — simulate a bet and see how commission is distributed across the upline chain

**Example:**
> Admin creates a BET game "Lotto" with code "lotto". Sets commission mode to "STATIC". Configures MASTERCLASS templates: Level 0 = 2.0%, Level 1 = 1.5%, Level 2 = 1.3%, etc. Player commission set to 0.2%. When a player bets 1,000 on Lotto, each agent in the upline receives their percentage.

**Backend endpoints:** `POST /api/commission/games`, `PUT /api/commission/config/{game_id}`, `PUT /api/commission/templates/{game_id}`, `PUT /api/commission/agent-settings/{game_id}`

---

### 2.13 Commission Monitor — ✅ WORKING

**Page:** `/commission-monitor`
**What it does:** View commission totals aggregated per level. Shows how much commission each level has earned across all games and bets.

**Backend endpoint:** `GET /api/commission/monitor`

---

### 2.14 Unpaid Collection — ✅ WORKING

**Page:** `/unpaid-collection`
**What it does:** Track agents who have unpaid credit balances. Shows each agent's total owed (from credit sends + credit requests) minus total paid (from payments). Only agents with a positive unpaid balance are listed.

**Example:**
> Agent ALICE received 5,000 in credits and has paid 2,000. Her unpaid balance is 3,000. She appears in the Unpaid Collection list.

---

### 2.15 Paid Collection — ✅ WORKING

**Page:** `/paid-collection`
**What it does:** Track agents who have fully paid their credit balances. Shows total owed, total paid, and remaining balance (zero or negative).

---

### 2.16 Withdrawal Request — ✅ WORKING

**Page:** `/withdrawal-request`
**What it does:** View pending withdrawal requests submitted by agents from the MasterClass Earnings page. Each request shows the agent, type (Standard/Specialty), amount, and status.

**Example:**
> Agent ROGER submits a 3,000 Standard withdrawal from the Earnings page. It appears here as "PENDING". Admin reviews and approves it.

**Backend endpoint:** `GET /api/withdrawal/`

---

### 2.17 Withdrawal Approved — ✅ WORKING

**Page:** `/withdrawal-approved`
**What it does:** View all withdrawal requests that have been approved. Shows the agent, amount, type, and approval date.

---

### 2.18 Withdrawal Disapproved — ✅ WORKING

**Page:** `/withdrawal-disapproved`
**What it does:** View all withdrawal requests that have been disapproved. Shows the agent, amount, type, and disapproval reason/date.

---

### 2.19 Masterclass Logs — ✅ WORKING

**Page:** `/logs`
**What it does:** View system activity logs in a paginated table. Each log entry shows timestamp, category (AGENT CREATION, ACCOUNT APPROVAL, ACCOUNT STATUS CHANGE, PASSWORD CHANGE, AGENT EDIT, etc.), performed by, target agent, IP address, MAC address, and details. Search/filter by target agent.

**Example:**
> Admin opens Logs and sees: "AGENT CREATION — Masterclass created Level 1 agent CARLOS (UID: 1234567890) — IP: 192.168.1.1".

**Backend endpoints:** `GET /api/logs/`, `GET /api/logs/agent/{id}`

---

### 2.20 Statistics — ⚠️ PARTIALLY WORKING

**Page:** `/statistics`
**What it does:** View system-wide statistics in a card grid. Shows:

| Stat | What it shows |
|---|---|
| Registration | Count today + total registered agents |
| Credit Request | Count + total amount |
| Credit Send | Count + total amount |
| Account Online | Online count + total approved |
| Account Offline | Offline count + total approved |
| Account Approval | Pending count + total |
| Unpaid Collection | Agents with unpaid + total unpaid amount |
| Default Player | Count |
| Non-Default Player | Count |
| Withdrawal Request | Count |
| Withdrawal Approved | Count |
| Withdrawal Disapproved | Count |

**What's missing:** 5 of the 12 stats (Default Player, Non-Default Player, Withdrawal Request, Withdrawal Approved, Withdrawal Disapproved) are hardcoded to `0` in the frontend and do not reflect actual backend data. The backend `/api/statistics/` endpoint returns all values, but the frontend doesn't display them all correctly.

**Backend endpoint:** `GET /api/statistics/`

---

## PART 3 — END-TO-END USER FLOWS

### Flow 1: New Agent Onboarding

```
Backoffice: Registration
  → Admin registers a Level 1 agent (MASTERCLASS scope)
  → Agent appears in Registration List with status "HOLD" (pending)

Backoffice: Account Approval
  → Admin reviews the agent's selfie-with-ID
  → Admin taps "Approve"
  → Agent status becomes "approved" (ACTIVE)

MasterClass: Login
  → Agent opens the app, enters phone + password
  → Lands on Dashboard
```

### Flow 2: Agent Creates Downline

```
MasterClass: Dashboard → Add Level
  → Agent fills form, takes selfie-with-ID
  → Sub-agent created at parent_level + 1
  → Sub-agent appears in Backoffice Registration List as "pending"

Backoffice: Account Approval
  → Admin approves the sub-agent
  → Sub-agent can now log in to MasterClass
```

### Flow 3: Credit Distribution

```
MasterClass (Child Agent): Credit Request
  → Child submits a credit request (e.g., 1,000 Standard)

MasterClass (Parent Agent): Credit Send
  → Parent sees the request in Credit Send page
  → Parent taps "Approve"
  → Credits are sent to the child

MasterClass (Parent Agent): Credit Send → Payment
  → Parent enters a payment amount and taps "Proceed to Pay"
  → Payment is recorded

Backoffice: Credit Request / Credit Send
  → Admin monitors all credit requests and sends
  → Admin views Unpaid Collection for agents with outstanding balances
```

### Flow 4: Commission Configuration

```
Backoffice: Commission Settings
  → Admin creates a game (e.g., "Lotto", type: BET)
  → Admin sets commission mode (STATIC / EDITABLE / DYNAMIC)
  → Admin edits commission templates per level per scope
  → Admin sets player commission percentage

Backoffice: Commission Monitor
  → Admin views commission totals per level after bets are placed
```

### Flow 5: Withdrawal Process

```
MasterClass: Earnings
  → Agent views Specialty / Standard earnings
  → Agent taps a wallet, enters amount, taps "Submit Withdrawal"
  → Withdrawal request created with status "PENDING"

Backoffice: Withdrawal Request
  → Admin sees the pending request
  → Admin approves or disapproves it

Backoffice: Withdrawal Approved / Disapproved
  → Approved/disapproved requests are viewable in their respective pages
```

### Flow 6: Player Registration

```
MasterClass: Dashboard → Register
  → Agent fills player form (name, mobile, birthdate, selfie-with-ID)
  → Player is created and linked to the agent

Backoffice: Default Player / Non-Default Player
  → Admin views registered players by scope
```

### Flow 7: Level Management

```
Backoffice: Level Creation
  → Admin sets max level for MASTERCLASS (e.g., 7)
  → Admin sets max level for DEFAULT (e.g., 2)
  → Admin toggles scope ONLINE/OFFLINE

MasterClass: Add Level
  → Agent attempts to create a sub-agent
  → If parent is already at max level, creation is blocked
```

### Flow 8: Account Management

```
Backoffice: Account Status
  → Admin views online/offline agents with earnings
  → Admin can set status: ACTIVE, HOLD, BLOCK

Backoffice: Logs
  → Admin views all activity logs (creation, approval, status change, password change, edits)
  → Admin can filter by target agent

Backoffice: Statistics
  → Admin views system-wide summary stats
```

---

## PART 4 — FEATURE QUICK REFERENCE

### Backoffice Routes

| Route | Page | Group | Status |
|---|---|---|---|
| `/features` | Features (landing) | — | ✅ Working |
| `/registration` | Registration | Agents | ✅ Working |
| `/registration-list` | Registration List | Agents | ✅ Working |
| `/agent-tree` | Agent Tree | Agents | ✅ Working |
| `/account-status` | Account Status | Agents | ✅ Working |
| `/approval` | Account Approval | Agents | ✅ Working |
| `/level-creation` | Level Creation | Levels | ✅ Working |
| `/default-player` | Default Player | Players | ✅ Working |
| `/non-default-player` | Non-Default Player | Players | ✅ Working |
| `/credit-request` | Credit Request | Credits | ✅ Working |
| `/credit-send` | Credit Send | Credits | ✅ Working |
| `/commission` | Commission Settings | Commission & Collection | ✅ Working |
| `/commission-monitor` | Commission Monitor | Commission & Collection | ✅ Working |
| `/unpaid-collection` | Unpaid Collection | Commission & Collection | ✅ Working |
| `/paid-collection` | Paid Collection | Commission & Collection | ✅ Working |
| `/withdrawal-request` | Withdrawal Request | Withdrawals | ✅ Working |
| `/withdrawal-approved` | Withdrawal Approved | Withdrawals | ✅ Working |
| `/withdrawal-disapproved` | Withdrawal Disapproved | Withdrawals | ✅ Working |
| `/logs` | Masterclass Logs | Logs | ✅ Working |
| `/statistics` | Statistics | Statistics | ⚠️ Partial (5/12 stats hardcoded to 0) |

### MasterClass Routes

| Route | Page | Auth Required | Status |
|---|---|---|---|
| `/login` | Login | No | ✅ Working (API) |
| `/reset` | Reset Password | No | ❌ Not implemented |
| `/dashboard` | Dashboard (Home) | Yes | ✅ Working (API) |
| `/add-level` | Add Level (Create Sub-Agent) | Yes | ✅ Working (API) |
| `/register` | Register Player | Yes | ✅ Working (API) |
| `/earnings` | Earnings | Yes | ✅ Working (API) |
| `/credit-send` | Credit Send | Yes | ✅ Working (API) |
| `/promo` | Promos | Yes | ❌ Not implemented (static buttons, no action) |
| `/support` | Support | Yes | ❌ Not implemented (hardcoded dummy data) |
| `/customer-support` | Customer Support | Yes | ❌ Not implemented (local chat only, no backend) |
| `/profile` | Profile | Yes | ✅ Working (API) |
| `/profile/share-referral` | Share Referral Code | Yes | ✅ Working (API) |
| `/selfie-with-id` | Selfie with ID | Yes | ✅ Working (API) |
| `/view-id` | View ID | Yes | ✅ Working (API) |
| `/theme` | Theme | Yes | ✅ Working (localStorage) |
| `/notifications` | Notifications | Yes | ⚠️ Partial (toggles work but not persisted) |
| `/settings` | Settings | Yes | ✅ Working (navigation menu) |
| `/profile-security` | Profile Security | Yes | ✅ Working (localStorage) |
| `/chat-settings` | Chat Settings | Yes | ✅ Working (localStorage) |
| `/chat-wallpaper` | Chat Wallpaper | Yes | ✅ Working (localStorage) |
| `/change-chat-name` | Change Chat Name | Yes | ✅ Working (localStorage) |
| `/app-icon` | App Icon | Yes | ✅ Working (localStorage) |
| `/deactivate-account` | Deactivate Account | Yes | ⚠️ Partial (localStorage only, no backend API) |
| `/change-password` | Change Password | Yes | ❌ Not implemented |
| `/link-player-account` | Link Player Account | Yes | ✅ Working (API) |

---

## PART 5 — REMAINING WORK TO REACH 100%

### ❌ Not Implemented (must build from scratch)

| # | Feature | What's needed | Layer |
|---|---------|---------------|-------|
| 1 | **Reset Password** (`/reset`) | Backend: OTP generation + verification endpoint, password reset endpoint. Frontend: Connect form to API. | Backend + Frontend |
| 2 | **Change Password** (`/change-password`) | Backend: Password change endpoint (with OTP or current password verification). Frontend: Connect form to API. | Backend + Frontend |
| 3 | **Promo** (`/promo`) | Backend: Promo CRUD API, promo participation/registration. Frontend: Promo detail pages, registration flow, dynamic loading. | Backend + Frontend |
| 4 | **Support** (`/support`) | Backend: Support ticket/chat API, message storage, agent-to-admin messaging. Frontend: Replace dummy data with real API calls, implement send. | Backend + Frontend |
| 5 | **Customer Support** (`/customer-support`) | Backend: Real-time chat API (WebSocket or polling), message persistence. Frontend: Connect to backend, persist messages. | Backend + Frontend |
| 6 | **Notification Settings API** | Backend: Endpoint to save/retrieve notification preferences per agent. | Backend |
| 7 | **Account Deactivation API** | Backend: Endpoint to set agent status to deactivated/blocked. Frontend: Call API instead of just clearing localStorage. | Backend + Frontend |

### ⚠️ Partially Working (must complete)

| # | Feature | What's needed |
|---|---------|---------------|
| 1 | **Statistics** (Backoffice) | Frontend: Wire 5 hardcoded stats (Default Player, Non-Default Player, Withdrawal Request/Approved/Disapproved) to actual backend values. Backend already returns some of these. |
| 2 | **Notifications** (MasterClass) | Frontend: Persist toggle state to localStorage or backend API so preferences survive page reloads. |
| 3 | **Deactivate Account** (MasterClass) | Frontend: Call backend API to actually deactivate the account, not just clear localStorage. |

### Completion Breakdown

```
Backend API:          90%  ████████████████████░░░  (44/49 features)
Backoffice Frontend:  97%  ██████████████████████░  (19.5/20 features)
MasterClass Frontend: 76%  ████████████████░░░░░░░  (19/25 features)
─────────────────────────────────────────────────────
OVERALL:              88%  ████████████████████░░░  (83/94 features)
```

**Remaining ~12% = 11 features** (9 not implemented + 2 partial fixes needed)
