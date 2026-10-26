# Product Requirements

> Maintained by QuorQ AI SDLC. Text between `sdlc:auto` markers is regenerated; anything outside them is yours and is kept.

<!-- sdlc:auto:start narrative -->
## Summary

### REQ-TECH-STACK · approved · source: requirements:v1

Product owner approved a fixed technology stack for this lightweight notepad app: FastAPI backend, AWS cloud hosting, PostgreSQL 16 database, React 19 frontend. The brief is explicit that the team must design against exactly these, not substitute.

Decisions as stated in requirements v1:
- Backend: FastAPI 0.115 -- chosen because server-side needs are modest (sign-in; create/edit/delete/search of one user's own notes); it is the platform default and keeps a small free app cheap to run.
- Cloud: AWS -- platform default, inexpensive hosting for modest individual usage, with room to grow.
- Database: PostgreSQL 16 -- notes are simple structured records (title, body, last-edited time) tied to an account; built-in full-text search can cover title/body search without an extra search service.
- Frontend: React 19 -- responsive single-page app for editing, searching, and a live recency-sorted list; platform default.
Explicitly out of scope: rich text formatting, folders/tags, checklists, image attachments, native mobile/desktop apps, offline editing/sync, and email/SMTP-based flows (verification, password reset).

**Related:** EPIC-001, EPIC-002, EPIC-003, EPIC-004

- ⚠ No architecture-phase input yet confirming how this approved stack will actually be structured into services, schema, or deployment topology.

### US-001 · draft · source: requirements:US-001

A new visitor can register a personal account with an email address and password to get a private space for notes. Priority: critical.

AC-001: Valid, not-already-registered email + password on submit creates the account and signs the user in. AC-002: Submitting an email that already has an account is rejected and the user is told the email is already in use. AC-003: Submitting with a missing/malformed email or missing password is rejected and the invalid field is identified. Assumption carried from EPIC-001: authentication is email+password only, with no email verification or password reset (SMTP was removed from the stack); the app is free with no billing.

**Related:** EPIC-001, AC-001, AC-002, AC-003

- ⚠ No password complexity/strength rule is specified in the input.

### US-002 · draft · source: requirements:US-002

A registered user signs in with email and password to reach their own notes list. Priority: critical.

AC-004: Correct email+password signs the user in and lands them on their notes list. AC-005: Incorrect email/password combination is rejected with a single generic invalid-credentials message that does not reveal which field was wrong (also specified as NFR-002).

**Related:** EPIC-001, AC-004, AC-005, NFR-002

### US-003 · draft · source: requirements:US-003

A signed-in user can sign out so notes aren't accessible to others on the same browser. Priority: high.

AC-006: Choosing sign-out ends the session and returns the user to the sign-in screen. AC-007: After signing out, attempting to open a notes page directly redirects to sign-in and shows no notes (ties to NFR-003).

**Related:** EPIC-001, AC-006, AC-007, NFR-003

### US-004 · draft · source: requirements:US-004

Notes functionality (list, view, editor) is available only to signed-in users. Priority: critical.

AC-008: An unauthenticated visitor attempting to reach any notes list, note view, or note editor is redirected to the sign-in screen with no note content shown. Directly implements NFR-003 and supports the privacy guarantee in NFR-001.

**Related:** EPIC-001, AC-008, NFR-001, NFR-003

### US-005 · draft · source: requirements:US-005

A signed-in user creates a new plain-text note with a title and body. Priority: critical.

AC-009: Entering a title and body and saving stores the note with an edited timestamp and shows it in the notes list. AC-010: Any typed formatting markup is stored and displayed as literal plain text -- no rich formatting is applied (rich text is explicitly out of scope). AC-011: A saved note belongs only to the creating account and is not visible to any other user.

**Related:** EPIC-002, AC-009, AC-010, AC-011

### US-006 · draft · source: requirements:US-006

A signed-in user opens one of their own notes and edits the title or body. Priority: critical.

AC-012: Saving a changed title/body stores the update and refreshes the note's most-recently-edited timestamp. AC-013: Discarding changes without saving leaves the note's previous title and body intact.

**Related:** EPIC-002, AC-012, AC-013

### US-007 · draft · source: requirements:US-007

A signed-in user deletes a note they no longer need, after confirming. Priority: high.

AC-014: Confirming deletion permanently removes the note from the account; it no longer appears in the list or search results. AC-015: Cancelling the confirmation leaves the note unchanged.

**Related:** EPIC-002, AC-014, AC-015

### US-008 · draft · source: requirements:US-008

Each signed-in user sees and can act on only their own notes -- nothing is shared or collaborative. Priority: critical.

AC-016: A user sees only the notes they own, never another user's notes. AC-017: Attempting to view, edit, or delete a note owned by another user -- even by guessing its identifier -- is denied and the note's content is not revealed. This is the core implementation of NFR-001.

**Related:** EPIC-002, AC-016, AC-017, NFR-001

### US-009 · draft · source: requirements:US-009

The notes list shows a signed-in user's notes ordered most-recently-edited first, with guidance when empty. Priority: high.

AC-018: Notes are listed from most recently edited to least recently edited. AC-019: Editing a note moves it to the top of the list on return. AC-020: When the user has no notes, an empty-state message invites them to create their first note.

**Related:** EPIC-003, AC-018, AC-019, AC-020

### US-010 · draft · source: requirements:US-010

A signed-in user searches their notes by text appearing in titles or bodies. Priority: high.

AC-021: Entering a search term filters the list to notes whose title or body contains it, still ordered by most-recently-edited. AC-022: A term matching nothing shows a no-results message. AC-023: Clearing the search term restores the full recency-sorted list. Requirements phase notes that PostgreSQL's built-in full-text search is intended to cover this without an extra search service.

**Related:** EPIC-003, AC-021, AC-022, AC-023

- ⚠ No architecture or development input yet confirms the search was actually implemented via PostgreSQL full-text search as anticipated.

### US-011 · draft · source: requirements:US-011

The app's layout adapts to desktop and mobile browser screens so notes can be read and managed comfortably on either. Priority: high.

AC-024: In a desktop browser, the notes list, search, and editor are laid out usably for a wide screen. AC-025: In a mobile browser, the same functionality is presented usably on a narrow screen with no horizontal scrolling and all actions remain reachable. Out of scope: native mobile/desktop apps and offline editing/sync.

**Related:** EPIC-004, AC-024, AC-025, NFR-004, NFR-005

### REQ-NFR-001 · draft · source: requirements:NFR-001

Privacy requirement: each user can see and act on only their own notes.

One user's notes are never shown to another; attempting to view, edit, or delete a note owned by someone else -- even by guessing its identifier -- is denied without revealing the note's content. Applies to US-008 and US-004.

**Related:** US-008, US-004

### REQ-NFR-002 · draft · source: requirements:NFR-002

Security requirement: sign-in failures show one generic message.

When sign-in details do not match a registered account, the app shows a single generic invalid-credentials message and never reveals whether the email or password was wrong. Applies to US-002.

**Related:** US-002

### REQ-NFR-003 · draft · source: requirements:NFR-003

Security requirement: notes pages are reachable only with a valid signed-in session.

All notes list, note view, and note editor pages are available only to a signed-in user; any attempt to reach them without a valid session -- directly or after signing out -- redirects to the sign-in screen and shows no note content. Applies to US-004 and US-003.

**Related:** US-004, US-003

### REQ-NFR-004 · draft · source: requirements:NFR-004

Compatibility requirement: works as a responsive web app in desktop and mobile browsers.

Delivered as a responsive web application that works in both desktop and mobile web browsers, with no native app or offline use. Applies to US-011.

**Related:** US-011

### REQ-NFR-005 · draft · source: requirements:NFR-005

Usability requirement: layout adapts to screen size with no horizontal scrolling.

The notes list, search, and editor adapt to screen size so that on a narrow phone screen all functions and actions stay reachable and usable without horizontal scrolling. Applies to US-011.

**Related:** US-011

### REQ-NFR-006 · draft · source: requirements:NFR-006

Accepted-risk scalability requirement: built only for modest individual-scale usage.

The app is built for modest individual-scale usage; no high-scale or heavy-load capacity has been designed in. This is explicitly called out as an accepted limitation for this lightweight build, applying to the whole system, not a defect to be fixed.
<!-- sdlc:auto:end narrative -->

<!-- sdlc:auto:start summary -->
**Requirements version:** v1  
**Delivery scope:** lightweight  
**Approved:** 2026-10-26

**Capabilities in scope:** Create, edit, and delete plain-text notes, Note title and text body, Searchable notes list over titles and bodies, Notes list sorted by most recently edited, Private per-user notes (each user sees only their own), Sign-in to a private personal account, Responsive layout for desktop and mobile browsers, Email-and-password account sign-in (no verification or reset emails)

**Approved technology stack:**

| Layer | Choice | Rationale |
|---|---|---|
| backend | FastAPI | The server-side work here is modest and well-defined: sign-in, and create/edit/delete/search of one user's own notes. FastAPI is a lightweight, fast way to build exactly these endpoints, is the platform default, and keeps a small free app cheap to run. |
| cloud | AWS | A small, free notepad with modest individual usage needs a reliable, inexpensive place to run the web app and database. AWS is the platform default and can host this cheaply, with plenty of room to grow if the app ever becomes popular — you are not boxed in. |
| database | PostgreSQL | Each user's notes are simple structured records (title, body, last-edited time) tied to an account, which is exactly what a relational database stores well. PostgreSQL also has built-in full-text search, so it can cover your search-by-title-and-body requirement without any extra search service, keeping the setup simple and free to run at individual scale. |
| frontend | React | Your notepad is a responsive web app that people use on both desktop and mobile browsers, with interactive screens for editing a note, searching, and a live list sorted by most recent edit. React handles this kind of snappy, single-page editing well and is the platform default, so there is no reason to deviate. |
<!-- sdlc:auto:end summary -->

<!-- sdlc:auto:start epics -->
## EPIC-001: Account & Authentication

Individual users sign in to a private personal account using an email address and password, so that each user can access only their own notes. Assumption: authentication is by email and password only; there is no email verification, password reset, or other email-based flows (SMTP was removed from the stack). Assumption: the app is free to use with no payments, plans, or billing.

### US-001: Register a personal account with email and password

**Priority:** critical

> As a new visitor, I want to create a personal account using my email address and a password, so that I can have a private space to keep my own notes.

| ID | Given | When | Then |
|---|---|---|---|
| AC-001 | I am on the registration screen and no account exists for my email | I enter a valid email address and a password and submit | my account is created and I am signed in to my private account |
| AC-002 | I am on the registration screen | I submit an email address that already has an account | registration is rejected and I am told the email is already in use |
| AC-003 | I am on the registration screen | I submit with a missing or malformed email, or a missing password | registration is rejected and I am shown which field is invalid |

### US-002: Sign in to my private account

**Priority:** critical

> As a registered user, I want to sign in with my email and password, so that I can access my own notes.

| ID | Given | When | Then |
|---|---|---|---|
| AC-004 | I have a registered account | I enter my correct email and password and submit | I am signed in and taken to my notes list |
| AC-005 | I am on the sign-in screen | I enter an email and password that do not match a registered account | sign-in is rejected and I am shown a generic invalid-credentials message without revealing which field was wrong |

### US-003: Sign out of my account

**Priority:** high

> As a signed-in user, I want to sign out, so that my notes are not accessible to anyone else using the same browser.

| ID | Given | When | Then |
|---|---|---|---|
| AC-006 | I am signed in | I choose to sign out | my session ends and I am returned to the sign-in screen |
| AC-007 | I have signed out | I try to open a notes page directly without signing in again | I am redirected to the sign-in screen and cannot see any notes |

### US-004: Restrict access to signed-in users only

**Priority:** critical

> As a user, I want notes functionality to be available only when I am signed in, so that my private notes are never shown to an unauthenticated visitor.

| ID | Given | When | Then |
|---|---|---|---|
| AC-008 | I am not signed in | I attempt to reach any notes list, note view, or note editor | I am redirected to the sign-in screen and no note content is shown |

## EPIC-002: Note Management

Signed-in users create, edit, and delete their own plain-text notes. Each note has a title and a plain-text body. Assumption: notes are plain text only — no rich text formatting, folders/tags, checklists, or image attachments.

### US-005: Create a plain-text note

**Priority:** critical

> As a signed-in user, I want to create a new note with a title and a plain-text body, so that I can capture what I want to remember.

| ID | Given | When | Then |
|---|---|---|---|
| AC-009 | I am signed in | I create a new note, enter a title and body text, and save it | the note is saved to my account with its title, body, and an edited timestamp, and appears in my notes list |
| AC-010 | I am creating a new note | I type formatting markup, it is treated purely as plain text | the note stores and displays the characters literally with no rich formatting applied |
| AC-011 | I am creating a new note | I save it, the saved note belongs only to my account | no other user can see it |

### US-006: Edit an existing note

**Priority:** critical

> As a signed-in user, I want to open one of my notes and change its title or body, so that I can keep my notes up to date.

| ID | Given | When | Then |
|---|---|---|---|
| AC-012 | I own a note and am signed in | I open it, change the title or body, and save | the updated title and body are stored and the note's most-recently-edited timestamp is updated |
| AC-013 | I am editing a note | I discard my changes without saving | the note retains its previous title and body |

### US-007: Delete a note

**Priority:** high

> As a signed-in user, I want to delete a note I no longer need, so that my notes list stays relevant.

| ID | Given | When | Then |
|---|---|---|---|
| AC-014 | I own a note and am signed in | I choose to delete it and confirm | the note is permanently removed from my account and no longer appears in my notes list or search results |
| AC-015 | I choose to delete a note | I cancel the confirmation | the note is kept unchanged |

### US-008: Keep notes private to their owner

**Priority:** critical

> As a signed-in user, I want to only ever see and act on my own notes, so that my notes stay private and nothing is shared or collaborative.

| ID | Given | When | Then |
|---|---|---|---|
| AC-016 | another user has their own notes | I am signed in to my account | I see only the notes I own and none belonging to any other user |
| AC-017 | I know or guess the identifier of a note owned by another user | I attempt to view, edit, or delete it | the action is denied and the note's content is not revealed |

## EPIC-003: Finding Notes

Users find their notes through a searchable list. Assumption: search matches text in note titles and bodies; the notes list is sorted by most recently edited.

### US-009: View my notes sorted by most recently edited

**Priority:** high

> As a signed-in user, I want to see a list of my notes ordered with the most recently edited first, so that I can quickly get back to what I was last working on.

| ID | Given | When | Then |
|---|---|---|---|
| AC-018 | I own several notes with different edited timestamps | I open my notes list | my notes are shown ordered from most recently edited to least recently edited |
| AC-019 | I edit a note | I return to the notes list | that note now appears at the top of the list |
| AC-020 | I have no notes yet | I open my notes list | I see an empty-state message inviting me to create my first note |

### US-010: Search my notes by title and body

**Priority:** high

> As a signed-in user, I want to search my notes by text that appears in their titles or bodies, so that I can find a specific note without scrolling the whole list.

| ID | Given | When | Then |
|---|---|---|---|
| AC-021 | I own notes whose titles or bodies contain a given term | I enter that term in the search box | the list shows only my notes whose title or body contains the term, still ordered by most recently edited |
| AC-022 | I have entered a search term | no note's title or body matches it | I am shown a no-results message |
| AC-023 | I have an active search | I clear the search term | the full notes list is shown again sorted by most recently edited |

## EPIC-004: Responsive Web Access

The product is delivered as a responsive web application usable in desktop and mobile browsers. Assumption: no native mobile or desktop app and no offline editing/sync. Assumption: designed for modest individual-scale usage with no high-scale constraints.

### US-011: Use the app on desktop and mobile browsers

**Priority:** high

> As a user, I want the app layout to adapt to the size of my screen, so that I can read and manage my notes comfortably on both a desktop and a phone.

| ID | Given | When | Then |
|---|---|---|---|
| AC-024 | I open the app in a desktop browser | the page renders | the notes list, search, and note editor are laid out usably for a wide screen |
| AC-025 | I open the app in a mobile browser | the page renders | the same functionality is presented in a layout usable on a narrow screen without horizontal scrolling, and all actions remain reachable |
<!-- sdlc:auto:end epics -->
