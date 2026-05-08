# Plataforma de Decisões Coletivas — Task Board

**28 tasks · 143h total · 6 weeks · Project I compliant**  
Class 2527 · ETIC_Algarve · Academic Year 2025/26

## Dev Container (partilhado)

Este repositório já inclui configuração de Dev Container em [.devcontainer/devcontainer.json](.devcontainer/devcontainer.json).

Para garantir o mesmo ambiente entre membros da equipa:
- Abrir o repositório no VS Code
- Executar **Dev Containers: Reopen in Container**
- Esperar o build inicial concluir

O container inclui Python, Node.js e Docker-in-Docker para desenvolvimento local com a mesma base para todos.

---

## Legend

| Domain | Colour |
|--------|--------|
| Backend | 🔵 Blue |
| Frontend | 🟢 Green |
| Shared | 🟡 Yellow |
| Infra | 🟣 Purple |

> Tasks marked **[NEW]** were added to cover Project I mandatory requirements (FR/NFR).

---

## Phase 1 — Foundation · Weeks 1–2 · 9 tasks · 34h

---

### T-17 · Shared · Dev A+B · 2h
**API contract documentation**  
> ⚠ Must complete before any Phase 1 coding begins.

Both devs define exact request/response shapes for all 7 endpoints. Document in `api-contract.md`. Agree on error formats, HTTP status codes, snake_case field names.

**Acceptance Criteria**
- All 7 endpoints fully documented
- Error format: `{error: str, field?: str}`
- All fields in snake_case
- Doc committed to repo root
- Both devs sign off

**Definition of Done**
- ✓ `api-contract.md` in repo
- ✓ No naming mismatches backend/frontend
- ✓ Reviewed in 30-min sync

---

### T-01 · Backend · Dev A · 4h
**Django project setup & config**

Initialise Django project with folder structure `collective-decisions/backend/`. Configure `settings.py` for dev/prod, install dependencies, write `requirements.txt`, setup SQLite.

**Acceptance Criteria**
- Runs locally with `manage.py runserver`
- `requirements.txt` with pinned versions
- Folder structure: `backend/`
- CORS configured for frontend origin
- Env vars via `.env` file

**Definition of Done**
- ✓ Code committed with `.gitignore`
- ✓ README has local setup steps
- ✓ Django admin at `/admin/` accessible
- ✓ No hard-coded secrets

---

### T-02 · Backend · Dev A · 5h
**Database models — Group, Decision, Vote**

Define all ORM models: Group (name, desc, admin FK), Decision (title, desc, deadline, group FK), Vote (user FK, option, score, decision FK). Run migrations.

**Acceptance Criteria**
- All 3 models created and migrated
- FK and ManyToMany relations correct
- `__str__` on all models
- Admin panel registers all models
- 1+ fixture file with seed data

**Definition of Done**
- ✓ Migrations clean — no errors
- ✓ Models reviewed by both devs
- ✓ Seed data loads via `loaddata`

---

### T-03 · Backend · Dev A · 6h
**JWT auth — register & login endpoints**

`POST /api/register` and `POST /api/login`. Validates unique email, hashes password. Login returns signed JWT. Use `djangorestframework-simplejwt`. Token expiry 24h.

**Acceptance Criteria**
- `POST /api/register` → 201 with user id
- `POST /api/login` → JWT on valid creds
- 400 with field errors on bad input
- Passwords never returned in responses
- Token expiry set to 24h

**Definition of Done**
- ✓ Unit tests for register + login pass
- ✓ Token verified in Postman
- ✓ Password stored as hash — confirmed

---

### T-09 · Frontend · Dev B · 6h
**Login & register pages**

Responsive login and register pages. Store JWT in `sessionStorage` on success, redirect to `/dashboard`. Show field-level API validation errors. Mobile-first.

**Acceptance Criteria**
- Login: email + password + submit
- Register: name, email, pwd, confirm
- Field errors shown below each input
- JWT stored in `sessionStorage`
- Redirect to `/dashboard` on success
- Loading state on submit button

**Definition of Done**
- ✓ Tested Chrome + Firefox
- ✓ Mobile verified at 375px
- ✓ No JWT in `localStorage`
- ✓ Error states manually tested

---

### T-10 · Frontend · Dev B · 7h
**Base layout, navigation & client-side routing**

Persistent shell with sidebar (Dashboard, Groups, Decisions, Profile), top bar, user avatar, logout. JS-based page routing, no full reloads. Active nav highlighting.

**Acceptance Criteria**
- Sidebar visible on all auth pages
- Nav routes without full reload
- Active route highlighted
- Logout clears `sessionStorage` + redirect
- Collapses to hamburger on mobile

**Definition of Done**
- ✓ Navigation tested all pages
- ✓ Logout flow verified
- ✓ Hamburger open/close works

---

### T-11 · Frontend · Dev B · 4h
**API client utility (fetch wrapper)**

Central `api.js` module wrapping all fetch calls. Auto-attaches `Authorization: Bearer` header. Handles 401 (logout redirect), 403 (permission error), network errors.

**Acceptance Criteria**
- All API calls through `api.js`
- JWT header auto-attached
- 401 triggers logout + redirect
- 403 shows permission error
- Network error shows toast

**Definition of Done**
- ✓ Tested: 200, 401, 403, 500
- ✓ No direct `fetch()` outside module
- ✓ Reviewed by Dev A

---

### T-20 · Infra · Dev A+B · 4h · [NEW]
**Docker & Docker Compose setup**  
`NFR-01, NFR-02`

Containerise the full stack. Write `Dockerfile` for Django. Write `docker-compose.yml` with django + db (PostgreSQL or SQLite volume). Ensure `docker compose up` brings the stack from a fresh clone with no manual steps.

**Acceptance Criteria**
- `docker compose up` starts without errors on a fresh clone
- Django container serves `/admin/` on localhost
- DB container (or SQLite volume) persists data between restarts
- `.env.example` lists every required env var
- No hard-coded credentials in `docker-compose.yml`

**Definition of Done**
- ✓ Tested on a fresh clone by both devs
- ✓ README install section uses `docker compose up`
- ✓ Committed to repo root

---

### T-21 · Shared · Dev A+B · 3h · [NEW]
**Scope document & Phase 1 deliverables**  
`Phase 1 deliverables`

Produce the Phase 1 artefacts required by the brief: scope document, roles & responsibilities, technical plan (stack, DB choice, Docker plan), design reference pinned, and presentation outline skeleton.

**Acceptance Criteria**
- Scope doc names the Web Design project being adapted
- Features in scope and out of scope explicitly listed
- Roles assigned to Dev A and Dev B
- DB choice (PostgreSQL/SQLite) justified in writing
- Design reference (Figma / screenshots) linked in repo
- Presentation outline skeleton committed

**Definition of Done**
- ✓ All artefacts committed by end of Week 2
- ✓ Tutor sign-off received
- ✓ README references the scope doc

---

## Phase 2 — Core Features · Weeks 3–4 · 11 tasks · 64h

---

### T-04 · Backend · Dev A · 7h
**Group & decision management endpoints**

CRUD for groups and decisions. `POST /api/group` sets requesting user as admin. `POST /api/decision` scoped to a group. Only members can create decisions. Deadline enforced.

**Acceptance Criteria**
- `POST /api/group` → 201 with group obj
- `POST /api/decision` → 201 with obj
- Non-members → 403
- `GET /api/group` → user's groups only
- Votes rejected after deadline

**Definition of Done**
- ✓ Tested: auth + unauth requests
- ✓ Permission logic code-reviewed
- ✓ Serializers validated

---

### T-05 · Backend · Dev A · 8h
**Voting API — 4 voting modes**

`POST /api/vote` supports 4 modes via `vote_type`: simple (radio), ranking (ordered), score (1-10), multi-choice. Duplicate votes rejected 409. Stores user, decision, option, score, timestamp.

**Acceptance Criteria**
- All 4 types accepted + validated
- Duplicate vote → 409
- Score validates 1-10 range
- Ranking: no duplicate positions
- Vote stored with full metadata

**Definition of Done**
- ✓ Each mode in Postman collection
- ✓ Edge cases tested
- ✓ Vote count visible in admin

---

### T-06 · Backend · Dev A · 5h
**Security — CSRF, permissions & groups**

Group-based permissions across all endpoints. Users act only within their groups. CSRF protection for non-JWT forms. Admin-only actions enforced (delete group, close decision).

**Acceptance Criteria**
- Unauth requests → 401
- Non-member access → 403
- Admin-only actions enforced
- CSRF on state-changing forms
- All endpoints require valid JWT

**Definition of Done**
- ✓ Security checklist completed
- ✓ Pen test: cross-group access → 403
- ✓ No sensitive data in errors

---

### T-12 · Frontend · Dev B · 10h
**Voting page UI — all 4 modes**  
> ⚠ Riskiest frontend task. Build simple/score/multi-choice first; drag-and-drop ranking last.

Adaptive voting UI by `vote_type`. Simple: radio. Ranking: drag-and-drop. Score: 1-10 slider per option. Multi-choice: checkboxes. Deadline countdown. Read-only if already voted.

**Acceptance Criteria**
- All 4 modes render by `vote_type`
- Score: slider with live numeric
- Ranking: drag to reorder options
- Voting disabled after deadline
- Confirmation on `POST /api/vote`
- Already-voted shown read-only

**Definition of Done**
- ✓ All 4 modes tested end-to-end
- ✓ Post-deadline state tested
- ✓ All inputs keyboard-navigable

---

### T-13 · Frontend · Dev B · 9h
**Group & decision management UI**

Create/view/manage groups and decisions. Modals for creation forms. List views with real-time client-side search and status filter (open/closed). Member list with roles.

**Acceptance Criteria**
- Create group: name + description
- Decision list: title, deadline, count, badge
- Group detail: members + roles
- Create decision: all fields incl. `vote_type`
- Search filters client-side in real-time

**Definition of Done**
- ✓ Forms tested with API errors
- ✓ Pages linked from navigation
- ✓ Empty states present

---

### T-14 · Frontend · Dev B · 6h
**Comments & justifications UI**

Comment section on decision detail page. Users post vote justifications. Shows avatar initials, username, timestamp. 500-char limit with live counter. Optimistic UI on submit.

**Acceptance Criteria**
- Comment form on decision page
- Chronological order, newest last
- 500-char limit with live counter
- Optimistic UI before API confirm
- Empty state when no comments

**Definition of Done**
- ✓ Submission tested with real API
- ✓ Counter works at 0, 250, 500
- ✓ Long content doesn't break layout

---

### T-22 · Backend · Dev A · 5h · [NEW]
**Django views, URLs & templates**  
`FR-01, FR-03, FR-05, FR-06`

Wire all main user flows through Django views, URLs and templates (or JSON endpoints consumed by frontend). CRUD for main domain entities must work end-to-end through the browser, not only via Postman.

**Acceptance Criteria**
- All main pages render via Django (templates or API + JS)
- Create / Read / Update / Delete work for Group and Decision from the browser
- Server-side input validation returns user-friendly errors
- No broken page flows (404, 500) on the happy path
- URLs follow REST conventions and are registered in `urls.py`

**Definition of Done**
- ✓ End-to-end flow tested in browser (not only Postman)
- ✓ Input validation tested: empty fields, bad types
- ✓ Code reviewed by partner

---

### T-23 · Frontend · Dev B · 6h · [NEW]
**Responsive layout & UX/UI fidelity**  
`FR-02, NFR-04, Criterion 5`

Ensure the delivered frontend matches the original Web Design artefact in layout, colours, typography, spacing and visual hierarchy. Verify responsiveness at desktop (1280px), tablet (768px) and mobile (375px).

**Acceptance Criteria**
- Layout matches design reference at all 3 breakpoints
- Correct colours, fonts and spacing from design tokens
- No horizontal scroll on mobile (375px)
- All interactive states (hover, focus, error) styled
- Keyboard navigation works on all forms

**Definition of Done**
- ✓ Screenshots at 375px / 768px / 1280px attached to PR
- ✓ Design reference side-by-side comparison done
- ✓ Accessibility: semantic HTML, alt text on images

---

### T-24 · Shared · Dev A+B · 2h · [NEW]
**Data model diagram (ER diagram)**  
`Criterion 4, Phase 2 deliverables`

Produce a clear Entity-Relationship diagram covering all domain entities (User, Group, Decision, Vote, Comment). Include fields, types, PKs, FKs and cardinalities. Commit to repo and reference in README.

**Acceptance Criteria**
- All entities and relationships shown
- PKs, FKs and cardinalities labelled
- Diagram matches actual Django models
- Committed as PNG or SVG to `/docs/`
- README links to the diagram

**Definition of Done**
- ✓ Reviewed by both devs
- ✓ Updated after any model change
- ✓ No mismatch with migrations

---

### T-25 · Shared · Dev A+B · 3h · [NEW]
**README & ongoing documentation**  
`NFR-06, NFR-07, Deliverables 2 & 3`

Write and maintain `README.md` throughout the project (not at the end). Must include: project summary, stack overview, installation via `docker compose up`, authors & contributions, AI Usage section, and link to design reference.

**Acceptance Criteria**
- Installation instructions work from a fresh clone
- AI Usage section lists every tool + purpose + scope
- Authors section maps each dev to their modules
- Stack section names Django version, DB, Docker
- Link to original Web Design artefact present
- User-facing note (half–1 page) on what the product does

**Definition of Done**
- ✓ README updated at each phase end, not only at delivery
- ✓ A teammate follows the install steps cold — succeeds
- ✓ AI Usage section complete before Phase 3

---

### T-18 · Shared · Dev A+B · 3h
**Integration testing — voting full flow**  
> ⚠ Run at end of Phase 2 before Phase 3. Block Phase 3 start if issues found.

E2E test: register > create group > create decision > vote > view results. Both devs run on their local machines. Document any mismatches as tickets.

**Acceptance Criteria**
- Full flow runs without errors
- Votes persisted — verified in admin
- Results endpoint returns winner
- JWT auth works all steps
- Run on both local machines

**Definition of Done**
- ✓ Checklist signed off both devs
- ✓ Bugs ticketed before Phase 3
- ✓ Scenario documented in repo

---

## Phase 3 — Intelligence · Weeks 5–6 · 8 tasks · 48h

---

### T-07 · Backend · Dev A · 10h
**Conflict detection algorithm + GET /api/conflicts**  
> ⚠ Riskiest backend task. Thresholds (variance>3.0, corr<-0.5) need real-data tuning in Phase 2.

Algorithm scans votes per decision: opposing votes (delta>6), divided groups (std dev>3.0), constant disagreers (correlation<-0.5), unstable decisions (vote changes>3). Returns severity per conflict.

**Acceptance Criteria**
- `GET /api/conflicts` → conflicts list
- Each conflict: type, severity, members
- Opposing votes: 2+ members at extremes
- Divided group: std dev > 3.0
- Response < 500ms for 50 members

**Definition of Done**
- ✓ Unit-tested with synthetic datasets
- ✓ 3+ conflict types verified E2E
- ✓ Results match manual calculation

---

### T-08 · Backend · Dev A · 10h
**Consensus engine + GET /api/results**

Weighted scoring algorithm finds most balanced option. Returns winning option, consensus score (0-100%), runner-up, intermediate recommendation. Works for all 4 vote types. Handles ties.

**Acceptance Criteria**
- `GET /api/results` → consensus object
- Score = inverse of vote variance
- Intermediate = weighted avg top 2
- Ties → both options + tie flag
- Works all 4 voting modes

**Definition of Done**
- ✓ Validated on 5 test scenarios
- ✓ Integrated + tested with frontend
- ✓ Single voter → 100% consensus

---

### T-15 · Frontend · Dev B · 10h
**Analytics dashboard with charts**

Main dashboard: vote distribution bar chart, participation rate card, consensus % badge, conflict indicator (green/amber/red). Chart.js. Decision switcher dropdown. Metric cards top row.

**Acceptance Criteria**
- Bar chart: vote distribution per option
- Consensus % badge colour-coded
- Conflict indicator from `/api/conflicts`
- Participation: X of Y voted
- Charts update on decision switch

**Definition of Done**
- ✓ Charts work with real API data
- ✓ 0 votes and 20+ votes tested
- ✓ Charts readable on mobile

---

### T-16 · Frontend · Dev B · 8h
**Conflict alerts & consensus recommendation UI**

Conflict panel: list with type, severity badges (low gray / medium amber / high red), affected members. Consensus panel: winner large, score bar, intermediate recommendation card.

**Acceptance Criteria**
- Conflict list: type, severity, members
- Severity: low=gray, med=amber, hi=red
- Winner shown large with percentage
- Intermediate recommendation card
- Empty state: 'No conflicts detected'

**Definition of Done**
- ✓ All 4 conflict types tested
- ✓ Tie scenario tested
- ✓ Responsive at 375px verified

---

### T-19 · Shared · Dev A+B · 4h
**Demo scenario, seed data & final polish**

Seed data for compelling demo: 2 groups, 5 decisions (all vote types), 10 users with varied votes triggering conflicts + clear consensus. One `manage.py` command. Final UI polish + bugs.

**Acceptance Criteria**
- Seed created with 1 `manage.py` cmd
- 1+ conflict auto-detected in demo
- 1+ clear winner in dashboard
- No console errors in demo run
- README updated with demo steps

**Definition of Done**
- ✓ Demo rehearsed end-to-end
- ✓ Both devs: UI presentation-ready
- ✓ Seed committed as fixtures

---

### T-26 · Shared · Dev A+B · 1h · [NEW]
**Git hygiene & tagged release**  
`NFR-05, Criterion 6, Section 7`

Ensure the repository has a clean, meaningful commit history throughout. Feature branches used, no dump commits. Tag the final release `v1.0.0` and open the PR to the reference repository before the deadline.

**Acceptance Criteria**
- All commits have descriptive messages (no 'fix', 'update', 'stuff')
- Feature branches used — no direct pushes to main
- PRs opened within the fork and reviewed by partner (pairs)
- Release tagged `v1.0.0` in Git
- Final PR opened to reference repo before 26 Jun 2026 23:59

**Definition of Done**
- ✓ No large dump commits in history
- ✓ Tag `v1.0.0` visible on GitHub
- ✓ PR to reference repo submitted
- ✓ Commit authorship traceable to each dev

---

### T-27 · Shared · Dev A+B · 2h · [NEW]
**Recorded product demo (2–4 min video)**  
`Deliverable 4, Phase 3`

Record a 2–4 minute screen-capture demo covering all main user flows: register, login, create group, create decision, vote, view results, conflict panel. Upload alongside the repository as a fallback for live-demo incidents.

**Acceptance Criteria**
- Video length 2–4 minutes
- Covers: register → login → group → decision → vote → results
- Audio narration or subtitles explaining each step
- No placeholder data — uses the seed from T-19
- Uploaded to repo or linked in README before deadline

**Definition of Done**
- ✓ Video watchable without extra software (MP4 or YouTube link)
- ✓ Both devs approve final cut
- ✓ README links to video

---

### T-28 · Shared · Dev A+B · 3h · [NEW]
**Presentation deck & final rehearsal**  
`Phase 3, Criterion 7, Section 9`

Build the presentation deck for the final session. Cover: problem, solution, architecture, live demo, AI usage transparency, lessons learned. Each dev must be able to explain every part of the code attributed to them.

**Acceptance Criteria**
- Deck covers problem / solution / architecture / demo / AI usage
- Each slide assigned to the dev who will present it
- AI Usage slide lists tools, purpose and scope honestly
- Full rehearsal done with live demo flow
- Deck committed to repo or linked in README

**Definition of Done**
- ✓ Rehearsal completed end-to-end at least once
- ✓ Both devs can answer Q&A on their code sections
- ✓ Deck finalised before the final session

---

## Summary

| Phase | Tasks | Hours |
|-------|-------|-------|
| Phase 1 — Foundation (Weeks 1–2) | 9 | 34h |
| Phase 2 — Core Features (Weeks 3–4) | 11 | 61h |
| Phase 3 — Intelligence (Weeks 5–6) | 8 | 48h |
| **Total** | **28** | **143h** |

| Assignee | Tasks | Hours |
|----------|-------|-------|
| Dev A | 9 | 55h |
| Dev B | 9 | 56h |
| Dev A+B (shared) | 10 | 32h |

**Critical path:** T-17 (API contract) → T-20 (Docker) → T-21 (Scope doc) must all be done before Phase 1 coding begins.  
**Integration gate:** T-18 must pass before Phase 3 starts.  
**Final deadline:** 26 June 2026, 23:59 WEST

---

*Plataforma de Decisões Coletivas · Class 2527 · ETIC_Algarve · 2025/26*