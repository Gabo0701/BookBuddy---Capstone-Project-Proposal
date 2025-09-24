# BookBuddy

A comprehensive book-tracking and library-management web app. Users discover books via ** OpenLibrary **, save them to personal collections, track progress with quick logs, and opt into **email reminders** to build a consistent reading habit.

> **Stack:** React 19 + Vite • Node.js + Express • MongoDB Atlas + Mongoose • JWT Auth • Tailwind • Nodemailer + Mailtrap  
> **Hosting:** Vercel (frontend) • Railway/Render (backend)

---

## Goal & Users
**Goal:** Provide a centralized, engaging way to discover, organize, and track reading—plus gentle email nudges to keep momentum.  
**Primary users:** Individual readers (18–45) who read ~12+ books/year.  
**Secondary users:** Students managing academic reading lists and book-club members.

---

## Data Plan
- **External source:** **Google Books API** (title, authors, ISBN, cover, description). Server-side proxy only—no API key in the browser.
- **Local data:** User accounts; minimal book snapshot when saved; user-specific data (status, notes, rating, favorites, collections, reading logs).

---

## Database Schema (brief)
- **users**: `{ _id, email(unique), passwordHash, preferences, isVerified, createdAt }`
- **books**: `{ _id, googleVolumeId(unique), isbn13?, title, authors[], coverUrl, description, apiSource:'google', createdAt }`
- **userBooks**: `{ _id, userId→users, bookId→books, status:'saved'|'reading'|'finished', progress{ currentPage?, percent? }, rating?, notes?, collections:[String], favorite:Boolean, startedAt?, finishedAt?, updatedAt }`
- **readingLogs**: `{ _id, userId, bookId, date, minutes?, pages?, note? }`  
Indexes: unique on `books.googleVolumeId`; compound unique `{ userId, bookId }` on `userBooks`.

---

## Key API Risks & Mitigations
- **Rate limits (~1k/day)** → backend proxy with **short-TTL cache**; client **debounced** search; **pagination**.
- **Missing/irregular fields** → normalization on the server; graceful UI fallbacks.
- **Security of API key** → server-only calls; **CORS allowlist**, **Helmet**, **rate limiting**.
- **Sessions** → **JWT access + refresh** with rotation/revocation.

---

## Security
- **bcrypt** password hashing; **JWT** (access/refresh); **Joi** validation & sanitization.
- **Helmet** headers, **CORS** (allowlisted FE origin), **rate limiting**, centralized error handler.
- Secrets in **environment variables**; HTTPS in production.
- **Email verification** and **password reset** (Mailtrap in dev).

---

## Core Functionality (MVP)
1. **Auth**: register → verify email (dev via Mailtrap) → login/logout → password reset.  
2. **Search & Discover**: Google Books search (pagination, loading/empty states).  
3. **Book Details**: normalized detail; **Add to Library**.  
4. **Personal Library**: filters (status, favorites, collections); inline updates (status/progress/rating/notes).  
5. **Reading Progress**: quick logs (minutes/pages) + visual indicator per book.  
6. **Email Reminders**: opt-in daily/weekly “time to read” emails.  
7. **Responsive UI**: mobile-first, accessible components.

**Stretch (time-boxed):** Advanced filters (author/subject/min rating); shareable public pages; Collections 2.0 (reorderable, color tags); PWA install/offline; simple reading analytics.

---

## User Flow
Landing → **Register/Login** → Dashboard → **Search** or **Library** → Book Detail → **Add** → Set **status/progress** → Logs accumulate → *(optional)* **reminders** → Finish book → **rate/review** → moves to **Completed**.

---

## Scope & Work Plan (≈ **50–60 hours**)
- Setup & env (repo, Vite/Express, Tailwind) — **3h** *(Easy, Full-stack, Must)*
- Schema & models — **2h** *(Medium, Backend, Must)*
- Auth (register/login/JWT, verify/reset) — **8h** *(Hard, Backend, Must)*
- Google Books proxy + cache — **4h** *(Medium, Backend, Must)*
- Search UI (debounce, pagination, states) — **6h** *(Medium, Frontend, Must)*
- Library management (add/remove, filters, notes/rating) — **8h** *(Medium, Full-stack, Must)*
- Progress & logs (UI + endpoints) — **6h** *(Medium, Full-stack, Must)*
- Email reminders (opt-in, schedule, templates) — **5h** *(Medium, Backend, Must)*
- Responsive polish & a11y — **8h** *(Medium, Frontend, Must)*
- Testing & deployment — **5h** *(Medium, Full-stack, Must)*  
**Stretch:** Advanced filters **3h**; Social sharing **6h**.

---

## Quick Start
**Prereqs:** Node 20+, MongoDB Atlas, Mailtrap account.

**Backend**
```bash
cd server
cp .env.example .env   # fill values below
npm i
npm run dev            # http://localhost:5000


## Diagrams
- [Flow Diagram (Architecture • User Flow • API flow)](docs/flow-diagram-bookbuddy.pdf)
