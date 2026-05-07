# Plataforma de Decisões Coletivas — Task Specification Board

**Project:** Collective Decisions Platform  
**Team:** 2 developers (Dev A — Backend, Dev B — Frontend)  
**Timeline:** ~6 weeks · 3 phases  
**Total estimated:** 114h (Dev A: 59h · Dev B: 55h · Shared: 9h · 19 tasks)

---

## Summary Table

| #    | Domain   | Title                                        | Assignee | Hours | Status |
|------|----------|----------------------------------------------|----------|-------|--------|
| T-01 | Backend  | Django project setup & config                | Dev A    | 4h    | To do  |
| T-02 | Backend  | Database models — Group, Decision, Vote      | Dev A    | 5h    | To do  |
| T-03 | Backend  | JWT auth — register & login endpoints        | Dev A    | 6h    | To do  |
| T-04 | Backend  | Group & decision management endpoints        | Dev A    | 7h    | To do  |
| T-05 | Backend  | Voting API — 4 voting modes                  | Dev A    | 8h    | To do  |
| T-06 | Backend  | Security — CSRF, permissions & groups        | Dev A    | 5h    | To do  |
| T-07 | Backend  | Conflict detection algorithm + GET /api/conflicts | Dev A | 10h  | To do  |
| T-08 | Backend  | Consensus engine + GET /api/results          | Dev A    | 10h   | To do  |
| T-09 | Frontend | Login & register pages                       | Dev B    | 6h    | To do  |
| T-10 | Frontend | Base layout, navigation & client-side routing| Dev B    | 7h    | To do  |
| T-11 | Frontend | API client utility (fetch wrapper)           | Dev B    | 4h    | To do  |
| T-12 | Frontend | Voting page UI — all 4 modes                 | Dev B    | 10h   | To do  |
| T-13 | Frontend | Group & decision management UI               | Dev B    | 9h    | To do  |
| T-14 | Frontend | Comments & justifications UI                 | Dev B    | 6h    | To do  |
| T-15 | Frontend | Analytics dashboard with charts              | Dev B    | 10h   | To do  |
| T-16 | Frontend | Conflict alerts & consensus recommendation UI| Dev B    | 8h    | To do  |
| T-17 | Shared   | API contract documentation                   | Dev A+B  | 2h    | To do  |
| T-18 | Shared   | Integration testing — voting full flow       | Dev A+B  | 3h    | To do  |
| T-19 | Shared   | Demo scenario, seed data & final polish      | Dev A+B  | 4h    | To do  |

---

## Phase 1 — Foundation (Weeks 1–2)

---

### T-01 · Backend · Django project setup & config

**Assignee:** Dev A  
**Hour estimation:** 4h  
**Status:** To do

**Description**  
Initialise the Django project with correct folder structure (`collective-decisions/backend/api/`). Configure `settings.py` for dev/prod environments, install dependencies, and write `requirements.txt`. Setup SQLite for development.

**Acceptance Criteria**
- Project runs locally with `python manage.py runserver`
- `requirements.txt` lists all dependencies with pinned versions
- Folder structure matches spec: `backend/api/`
- CORS headers configured to accept frontend origin
- Environment variables loaded via `.env`

**Definition of Done**
- Code committed to repo with `.gitignore`
- README contains local setup instructions
- Django admin accessible at `/admin/`
- No hard-coded secrets in codebase

---

### T-02 · Backend · Database models — Group, Decision, Vote

**Assignee:** Dev A  
**Hour estimation:** 5h  
**Status:** To do

**Description**  
Define all ORM models with correct field types, relationships, and constraints. `Group` has name, description, admin FK. `Decision` has title, description, deadline, group FK. `Vote` has user FK, option, score, decision FK.

**Acceptance Criteria**
- All 3 models created and migrated
- ForeignKey and ManyToMany relations correct
- `__str__` methods defined on all models
- Admin panel registers all models
- At least 1 fixture file with seed data

**Definition of Done**
- Migrations applied without errors
- Models reviewed and approved by both devs
- Seed data loadable via `manage.py loaddata`

---

### T-03 · Backend · JWT auth — register & login endpoints

**Assignee:** Dev A  
**Hour estimation:** 6h  
**Status:** To do

**Description**  
Implement `POST /api/register` and `POST /api/login`. Register validates unique email and hashes password with Django hasher. Login returns a signed JWT access token. Use `djangorestframework-simplejwt`.

**Acceptance Criteria**
- `POST /api/register` returns 201 with user id
- `POST /api/login` returns JWT token on valid credentials
- Returns 400 with field errors on invalid input
- Passwords never returned in any response
- Token expiry set to 24h

**Definition of Done**
- Unit tests for register + login pass
- Token verified in Postman/curl
- Password stored as hash in DB — confirmed

---

### T-09 · Frontend · Login & register pages

**Assignee:** Dev B  
**Hour estimation:** 6h  
**Status:** To do

**Description**  
Build responsive login and register HTML/CSS/JS pages. On successful login, store JWT in `sessionStorage` and redirect to dashboard. Show field-level validation errors from API. Mobile-first layout.

**Acceptance Criteria**
- Login form: email + password fields, submit button
- Register form: name, email, password, confirm password
- Field validation errors displayed below each input
- JWT stored in `sessionStorage` after login
- Redirect to `/dashboard` on success
- Loading state on submit button during API call

**Definition of Done**
- Tested on Chrome + Firefox
- Mobile layout verified at 375px width
- No JWT stored in `localStorage` (security requirement)
- Error states manually tested with wrong credentials

---

### T-10 · Frontend · Base layout, navigation & client-side routing

**Assignee:** Dev B  
**Hour estimation:** 7h  
**Status:** To do

**Description**  
Build the persistent shell: sidebar navigation with links (Dashboard, Groups, Decisions, Profile), top bar with user avatar and logout. Implement JS-based page routing without full reloads. Active nav state highlighting.

**Acceptance Criteria**
- Sidebar visible on all authenticated pages
- Nav links route to correct sections without full reload
- Active route highlighted in sidebar
- Logout clears `sessionStorage` and redirects to login
- Responsive: sidebar collapses to hamburger menu on mobile

**Definition of Done**
- Navigation tested across all pages
- Logout flow verified — token cleared
- Mobile hamburger menu opens and closes correctly

---

### T-11 · Frontend · API client utility (fetch wrapper)

**Assignee:** Dev B  
**Hour estimation:** 4h  
**Status:** To do

**Description**  
Create a central `api.js` module that wraps all fetch calls. Automatically attaches `Authorization: Bearer <token>` header. Handles 401 (redirect to login), 403 (show permission error), and network errors. All API calls go through this module.

**Acceptance Criteria**
- All API calls route through `api.js`
- JWT header attached automatically to every request
- 401 response triggers logout and redirect
- 403 response shows user-friendly permission denied message
- Network error shows toast notification

**Definition of Done**
- Module tested with mock responses for 200, 401, 403, 500
- No direct `fetch()` calls outside this module
- Code reviewed by Dev A for API contract alignment

---

### T-17 · Shared · API contract documentation

**Assignee:** Dev A + Dev B  
**Hour estimation:** 2h  
**Status:** To do

> ⚠️ **Must be completed before any other Phase 1 backend/frontend work begins.**

**Description**  
Both devs jointly define the exact request/response shape for all 7 API endpoints before coding begins. Document in a shared `api-contract.md`. Agree on error formats, status codes, and field names.

**Acceptance Criteria**
- All 7 endpoints documented with method, URL, request body, response body
- Error format standardised: `{ error: string, field?: string }`
- Field names agreed (snake_case throughout)
- Document committed to repo root
- Both devs sign off before Phase 2 begins

**Definition of Done**
- `api-contract.md` in repo
- No naming discrepancies between backend serializers and frontend fetch calls
- Reviewed in a 30-min sync session

---

## Phase 2 — Core Features (Weeks 3–4)

---

### T-04 · Backend · Group & decision management endpoints

**Assignee:** Dev A  
**Hour estimation:** 7h  
**Status:** To do

**Description**  
CRUD endpoints for groups and decisions. `POST /api/group` creates a group with the requesting user as admin. `POST /api/decision` creates a decision scoped to a group. Only group members can create decisions.

**Acceptance Criteria**
- `POST /api/group` returns 201 with group object
- `POST /api/decision` returns 201 with decision object
- Non-members receive 403 on decision creation
- `GET /api/group` lists only groups the user belongs to
- Decision respects deadline field and rejects votes after deadline

**Definition of Done**
- All endpoints tested with authenticated + unauthenticated requests
- Permission logic code-reviewed
- Serializers validated for required fields

---

### T-05 · Backend · Voting API — 4 voting modes

**Assignee:** Dev A  
**Hour estimation:** 8h  
**Status:** To do

**Description**  
Implement `POST /api/vote` supporting 4 modes: simple (single option), ranking (ordered list), score (1–10 integer), multi-choice (multiple options). Mode is determined by the Decision's `vote_type` field.

**Acceptance Criteria**
- All 4 vote types accepted and validated
- Duplicate votes from same user on same decision rejected (409)
- Score validates range 1–10
- Ranking validates no duplicate positions
- Vote stored with user, decision, option, score, timestamp

**Definition of Done**
- Each mode tested via Postman collection
- Edge cases tested: out-of-range score, duplicate vote
- Vote count verifiable in admin panel

---

### T-06 · Backend · Security — CSRF, permissions & groups

**Assignee:** Dev A  
**Hour estimation:** 5h  
**Status:** To do

**Description**  
Apply group-based permission model across all endpoints. Authenticated users can only act within groups they belong to. Enable CSRF protection for non-JWT form submissions. Document all permission rules.

**Acceptance Criteria**
- Unauthenticated requests return 401
- Non-member access returns 403
- Admin-only actions (delete group, close decision) enforced
- CSRF token required for state-changing form requests
- All endpoints require valid JWT header

**Definition of Done**
- Security review checklist completed
- Penetration test: try accessing another user's group — confirm 403
- No sensitive data leaked in error responses

---

### T-12 · Frontend · Voting page UI — all 4 modes

**Assignee:** Dev B  
**Hour estimation:** 10h  
**Status:** To do

> ⚠️ **Riskiest frontend task.** Implement simple, score, and multi-choice modes first. Tackle drag-and-drop ranking last.

**Description**  
Build the voting interface that adapts its UI based on the decision's `vote_type`. Simple: radio buttons. Ranking: drag-and-drop ordered list. Score: 1–10 slider per option. Multi-choice: checkboxes. Show deadline countdown.

**Acceptance Criteria**
- All 4 voting modes render correctly based on `vote_type`
- Score mode: slider updates live numeric display
- Ranking mode: options reorderable by drag
- Deadline countdown shown, voting disabled after deadline
- Submit confirmation shown after successful `POST /api/vote`
- Already-voted state: shows user's previous vote read-only

**Definition of Done**
- All 4 modes tested end-to-end with backend
- Post-deadline state tested with a past deadline
- Accessibility: all inputs keyboard-navigable

---

### T-13 · Frontend · Group & decision management UI

**Assignee:** Dev B  
**Hour estimation:** 9h  
**Status:** To do

**Description**  
Pages for creating groups, viewing group member lists, creating decisions, and viewing decision details. Include modals for creation forms. List views with search and filter by status (open/closed).

**Acceptance Criteria**
- Create group form: name + description fields
- Decision list shows title, deadline, vote count, status badge
- Group detail page lists all members with their role
- Create decision form: title, description, deadline, vote_type
- Search input filters decision list in real-time (client-side)

**Definition of Done**
- Forms tested with validation errors from API
- Group and decision pages linked from navigation
- Empty states shown when no groups/decisions exist

---

### T-14 · Frontend · Comments & justifications UI

**Assignee:** Dev B  
**Hour estimation:** 6h  
**Status:** To do

**Description**  
Add comment section below each decision detail page. Users can post text justifications for their vote. Comments show username, avatar initials, timestamp, and text. New comment POST request on submit.

**Acceptance Criteria**
- Comment form visible on decision detail page
- Comments display in chronological order, newest last
- Character limit of 500 enforced with live counter
- Optimistic UI: comment appears immediately before API confirms
- Empty state shown when no comments exist

**Definition of Done**
- Comment submission tested with real API
- Character counter works at boundaries (0, 250, 500)
- Long names and long comments don't break layout

---

### T-18 · Shared · Integration testing — voting full flow

**Assignee:** Dev A + Dev B  
**Hour estimation:** 3h  
**Status:** To do

**Description**  
End-to-end test of the complete voting flow: register → create group → create decision → vote → view results. Both devs run this together at end of Phase 2 to catch mismatches before Phase 3.

**Acceptance Criteria**
- Full flow executes without errors in browser
- Votes persisted in DB — verified in Django admin
- Results endpoint returns correct winner
- JWT auth works across all steps
- Test run on both devs' local machines

**Definition of Done**
- Test checklist signed off by both devs
- Any bugs found are ticketed before Phase 3 starts
- Test scenario documented in repo

---

## Phase 3 — Intelligence (Weeks 5–6)

---

### T-07 · Backend · Conflict detection algorithm + GET /api/conflicts

**Assignee:** Dev A  
**Hour estimation:** 10h  
**Status:** To do

> ⚠️ **Riskiest backend task.** Algorithm thresholds (variance > 3.0, correlation < -0.5) are starting points — expect tuning once real voting data is available.

**Description**  
Implement algorithm that scans votes per decision and identifies: opposing votes (score delta > 6), divided groups (variance > threshold), members in constant disagreement (correlation < -0.5 across decisions), and unstable decisions (vote changes > 3).

**Acceptance Criteria**
- `GET /api/conflicts` returns list of conflict objects per decision
- Each conflict has: type, severity (low/medium/high), affected members
- Opposing vote detection triggers when 2+ members score opposite ends
- Divided group detected when std deviation of scores > 3.0
- Response time under 500ms for groups up to 50 members

**Definition of Done**
- Algorithm unit-tested with synthetic vote datasets
- At least 3 conflict types verified end-to-end
- Results match manual calculation on test data

---

### T-08 · Backend · Consensus engine + GET /api/results

**Assignee:** Dev A  
**Hour estimation:** 10h  
**Status:** To do

**Description**  
Implement weighted scoring algorithm that finds the most balanced option across all votes. Returns: winning option, consensus score (0–100%), runner-up, and recommended intermediate solution. Handles all 4 vote types.

**Acceptance Criteria**
- `GET /api/results` returns consensus object with winning option
- Consensus score calculated as inverse of vote variance
- Intermediate recommendation logic: weighted average of top 2 options
- Handles ties by returning both options with tie flag
- Works correctly for all 4 voting modes

**Definition of Done**
- Algorithm validated against 5 pre-defined test scenarios
- Results endpoint integrated and tested with frontend
- Edge case: single voter — returns 100% consensus

---

### T-15 · Frontend · Analytics dashboard with charts

**Assignee:** Dev B  
**Hour estimation:** 10h  
**Status:** To do

**Description**  
Build the main dashboard showing vote distribution charts, participation rate, consensus level gauge, and conflict indicator per decision. Use Chart.js. Metric cards for key stats at the top.

**Acceptance Criteria**
- Bar chart shows vote distribution per option
- Consensus level shown as percentage badge (colour-coded)
- Conflict indicator: green/amber/red based on `/api/conflicts` severity
- Participation card: X of Y members voted
- Charts update when user switches between decisions via dropdown

**Definition of Done**
- Charts render correctly with real API data
- Tested with 0 votes (empty state) and 20+ votes
- Charts are readable on mobile (responsive canvas)

---

### T-16 · Frontend · Conflict alerts & consensus recommendation UI

**Assignee:** Dev B  
**Hour estimation:** 8h  
**Status:** To do

**Description**  
Surface conflict and consensus data visually. Conflict panel lists detected conflicts with severity badges and affected member names. Consensus panel shows winning option prominently with score bar and intermediate recommendation.

**Acceptance Criteria**
- Conflict panel shows list of conflicts with type, severity, members
- Severity badge: low (gray), medium (amber), high (red)
- Consensus winner shown with large option name + percentage
- Intermediate recommendation shown as a secondary card
- "No conflicts detected" message when `/api/conflicts` returns empty

**Definition of Done**
- UI tested with all 4 conflict types from backend
- Consensus panel tested with tie scenario
- Responsive layout verified on 375px

---

### T-19 · Shared · Demo scenario, seed data & final polish

**Assignee:** Dev A + Dev B  
**Hour estimation:** 4h  
**Status:** To do

**Description**  
Prepare a compelling demo with realistic seed data: 2 groups, 5 decisions across different vote types, 10 users with varied votes to trigger both conflicts and a clear consensus. Final UI polish and bug fixes.

**Acceptance Criteria**
- Seed script creates demo data with one `manage.py` command
- At least 1 conflict detected automatically in demo data
- At least 1 clear consensus winner visible in dashboard
- No console errors in final demo run
- README updated with demo instructions

**Definition of Done**
- Demo run rehearsed end-to-end
- Both devs agree UI is presentation-ready
- Seed data committed to repo as fixtures

---

## Tech Lead Notes

**Critical path:** T-17 (API contract) must be completed first — it unblocks both devs working in parallel from day one.

**Watch tasks:** T-07 (conflict detection) has algorithm thresholds that will need real-data tuning. T-12 (voting UI) is the most complex frontend task — prioritise the 3 simpler voting modes before tackling drag-and-drop ranking.

**Integration checkpoints:** Run T-18 (integration test) at the end of Phase 2 before starting any Phase 3 intelligence work to catch API mismatches early.