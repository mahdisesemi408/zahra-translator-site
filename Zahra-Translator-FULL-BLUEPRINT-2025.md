# Zahra Ejraei — Professional Translator Website
## FULL BLUEPRINT (Architecture + Security + Design + Workflow)
**Version:** 2025-11 + HomePage UI Addendum (2025-12)

---

## 0. IMPORTANT INSTRUCTIONS FOR ANY AI / CODING AGENT

This file fully describes the project I want to build.

Any AI system, coding agent, or assistant MUST:

1. **Read this file completely** before generating or modifying any code.
2. Treat this file as the **single source of truth** for:
   - What the project is
   - Tech stack
   - Architecture and folder structure
   - Database and APIs
   - Security mindset
   - Frontend–Backend–Admin connections
   - External services (Supabase, Vercel, GitHub, etc.)
   - Human steps (where the human must go to a website and do something manually)
3. If anything is missing or unclear:
   - Make **safe, reasonable assumptions**
   - Explicitly list those assumptions in a short bullet list.
4. Whenever some step requires manual work by the human:
   - Clearly say:
     - Which website or tool to open (Supabase, Vercel, GitHub, etc.)
     - Which exact steps or commands to run
     - Which environment variables or config keys to set
     - In which file they should be placed.
5. **UI Copy Rule (strict):**
   - Do **NOT** put meta-commentary inside the UI text (forbidden examples: “امنیت واقعی سمت سرور…”, “روی کادر کلیک کنید…”, “این فقط UI است…”).
   - Security notes belong in **developer comments** or **docs**, not user-facing text.

---

## 1. High-Level Project Summary

- **Owner:** Zahra Ejraei — professional translator & researcher.
- **Goal:** Build a **world-class personal website** that combines:
  - Online resume / CV
  - Portfolio of translation work
  - Blog (articles, research notes)
  - Booking system for consultations
  - Client testimonials
  - Admin panel for full control

- **Target quality:** 2025-grade, modern, secure, fast, and beautiful.

### 1.1. Core Features

- Public:
  - Home page with hero, services, highlights, CTAs
  - About page (bio, education, experience)
  - Services page (+ per-service detail pages)
  - Portfolio (works listing + detail pages)
  - Blog (posts listing + post detail)
  - Resume page (online CV + download PDF)
  - Booking page (consultation booking form/flow)
  - Testimonials page (client feedback)
  - Contact page (contact form)
  - Legal pages (Privacy, Terms)
- Admin:
  - Dashboard overview
  - Bookings management
  - Works (portfolio) CRUD
  - Blog posts CRUD
  - Testimonials moderation
  - Contact messages list and status
  - Settings (site-wide configuration)
  - Admin login

### 1.2. Tech Stack

- **Framework:** Next.js 15+ (App Router)
- **Language:** TypeScript
- **Frontend:**
  - React
  - Tailwind CSS
  - Component library: shadcn/ui (or similar)
- **Backend:**
  - Next.js Route Handlers in `/app/api`
- **Database:**
  - PostgreSQL (Supabase / Neon / Railway)
  - ORM: Prisma
- **Deployment:** Vercel
- **Storage:**
  - Phase 1: local `public/uploads`
  - Phase 2: S3-compatible storage (Supabase Storage / R2 / S3)

### 1.3. Non-Functional Requirements

- Performance:
  - Core Web Vitals: “Good”
  - Lighthouse:
    - Performance ≥ 95
    - SEO ≥ 95
- Security:
  - OWASP-style mindset:
    - Strong validation
    - Secure auth
    - Rate limiting
    - No raw SQL
- UX & Design:
  - Mobile-first, fully responsive
  - Modern, minimal, elegant
  - 3 theme modes: **Light / Dark / Ultra-Dark**
  - Style vibe: premium gradient + glass UI, readable and calm (mobile friendly)
- Languages:
  - Primary UI: **Persian (fa-IR, RTL)**
  - Optional: English (en-US, LTR)

---

## 2. Overall Architecture: One Project, Three Logical Layers

We use **one Next.js project (monorepo)** with three internal logical layers:

1. **Public Frontend UI**
   - Located in: `app/(fa)` (and optionally `app/(en)`)
   - Contains all public pages and layouts
2. **Admin Panel UI**
   - Located in: `app/admin`
   - Contains admin views (dashboard, CRUD, settings, login)
3. **Backend (API Layer)**
   - Located in: `app/api`
   - Contains public and admin API endpoints
   - Talks to the database via Prisma

Everything lives in a **single repo**, but we still keep clear separation by folders and naming.

---

## 3. Canonical Project Folder Structure (MUST BE FOLLOWED)

```text
/
├─ app/
│  ├─ (fa)/                       # Persian (fa-IR, RTL) public site
│  │  ├─ layout.tsx               # Root layout for fa (sets dir="rtl", lang="fa")
│  │  ├─ page.tsx                 # Home (fa)
│  │  ├─ about/page.tsx
│  │  ├─ services/page.tsx
│  │  ├─ services/[slug]/page.tsx
│  │  ├─ portfolio/page.tsx
│  │  ├─ portfolio/[slug]/page.tsx
│  │  ├─ blog/page.tsx
│  │  ├─ blog/[slug]/page.tsx
│  │  ├─ resume/page.tsx
│  │  ├─ booking/page.tsx
│  │  ├─ testimonials/page.tsx
│  │  ├─ contact/page.tsx
│  │  ├─ privacy/page.tsx
│  │  ├─ terms/page.tsx
│  │  └─ (local components if needed)
│  │
│  ├─ (en)/                       # optional English version (mirror of fa)
│  │  └─ ...
│  │
│  ├─ admin/                      # Admin panel UI (protected)
│  │  ├─ layout.tsx
│  │  ├─ page.tsx
│  │  ├─ bookings/page.tsx
│  │  ├─ bookings/[id]/page.tsx
│  │  ├─ works/page.tsx
│  │  ├─ works/new/page.tsx
│  │  ├─ works/[id]/page.tsx
│  │  ├─ posts/page.tsx
│  │  ├─ posts/new/page.tsx
│  │  ├─ posts/[id]/page.tsx
│  │  ├─ testimonials/page.tsx
│  │  ├─ contacts/page.tsx
│  │  ├─ settings/page.tsx
│  │  └─ (admin-only components)
│  │
│  ├─ admin/login/page.tsx
│  │
│  ├─ api/                        # Backend API layer (public + admin)
│  │  ├─ contact/route.ts
│  │  ├─ bookings/route.ts
│  │  ├─ testimonials/route.ts
│  │  ├─ posts/route.ts
│  │  ├─ posts/[slug]/route.ts
│  │  ├─ works/route.ts
│  │  ├─ works/[slug]/route.ts
│  │  ├─ auth/login/route.ts
│  │  ├─ auth/logout/route.ts
│  │  ├─ admin/bookings/route.ts
│  │  ├─ admin/bookings/[id]/route.ts
│  │  ├─ admin/works/route.ts
│  │  ├─ admin/works/[id]/route.ts
│  │  ├─ admin/posts/route.ts
│  │  ├─ admin/posts/[id]/route.ts
│  │  ├─ admin/testimonials/route.ts
│  │  ├─ admin/contacts/route.ts
│  │  ├─ admin/settings/route.ts
│  │  ├─ sitemap.ts
│  │  └─ robots.txt
│  │
│  ├─ error.tsx
│  └─ not-found.tsx
│
├─ components/
│  ├─ layout/                     # Navbar, Footer, Shells
│  ├─ ui/                         # Buttons, Inputs, Dropdowns, Modals, etc.
│  ├─ sections/                   # Hero, ServicesOverview, PortfolioGrid, etc.
│  ├─ forms/                      # BookingForm, ContactForm, etc.
│  └─ admin/                      # Admin tables, filters, layouts
│
├─ lib/
│  ├─ db.ts                       # Prisma client (singleton)
│  ├─ validators/                 # Zod schemas for forms and APIs
│  │  ├─ contact.ts
│  │  ├─ booking.ts
│  │  ├─ testimonial.ts
│  │  └─ auth.ts
│  ├─ auth.ts                     # Auth helpers (session, requireAdmin, etc.)
│  ├─ booking.ts                  # Booking domain logic
│  ├─ seo.ts                      # SEO helpers (metadata, OG tags)
│  ├─ i18n.ts                     # Optional i18n helpers for fa/en
│  └─ utils.ts                    # Small utility helpers
│
├─ prisma/
│  ├─ schema.prisma
│  └─ migrations/
│
├─ public/
│  ├─ images/
│  └─ uploads/                    # Phase 1
│
├─ styles/
│  └─ globals.css
│
├─ AGENT.md
├─ ARCHITECTURE_Zahra-Translator.v2.md
├─ MASTER_WORKFLOW_PROMPT.md
├─ Zahra-Translator-FULL-BLUEPRINT-2025.md  # This file
├─ .env.local
├─ package.json
├─ tsconfig.json
├─ next.config.mjs
└─ README.md
```

---

## 4. Data Model (Conceptual Summary)

The Prisma schema must implement at least:

### 4.1. User (Admin)
- `id: string (UUID)`
- `name: string`
- `email: string` (unique)
- `passwordHash: string`
- `role: enum("ADMIN")`
- `createdAt: DateTime`
- `updatedAt: DateTime`

### 4.2. Booking
- `id: string (UUID)`
- `clientName: string`
- `clientEmail: string`
- `clientPhone: string | null`
- `type: string`
- `durationMinutes: int`
- `startDateTime: DateTime`
- `status: enum("PENDING","CONFIRMED","CANCELLED","COMPLETED")`
- `notes: string | null`
- `fileUrl: string | null`
- `sourceIp: string | null`
- `userAgent: string | null`
- `createdAt: DateTime`
- `updatedAt: DateTime`

### 4.3. Work (Portfolio)
- `id: string (UUID)`
- `slug: string` (unique)
- `title: string`
- `summary: string`
- `description: string`
- `domain: string`
- `languagePair: string`
- `projectType: string`
- `clientName: string | null`
- `publicUrl: string | null`
- `coverImageUrl: string | null`
- `createdAt: DateTime`
- `updatedAt: DateTime`

### 4.4. BlogPost
- `id: string (UUID)`
- `slug: string (unique)`
- `title: string`
- `excerpt: string`
- `content: string` (Markdown / rich text)
- `coverImageUrl: string | null`
- `publishedAt: DateTime | null`
- `isPublished: boolean`
- `createdAt: DateTime`
- `updatedAt: DateTime`

### 4.5. Testimonial
- `id: string (UUID)`
- `clientName: string`
- `projectType: string`
- `rating: int (1–5)`
- `message: string`
- `isApproved: boolean`
- `createdAt: DateTime`

### 4.6. ContactMessage
- `id: string (UUID)`
- `name: string`
- `email: string`
- `subject: string`
- `message: string`
- `status: enum("NEW","READ","REPLIED")`
- `createdAt: DateTime`

### 4.7. Setting
- `id: string (UUID)`
- `key: string (unique)`
- `value: string (JSON/text)`
- `updatedAt: DateTime`

Required Settings keys (must exist):
- `site.city.fa` = "شیراز"
- `site.contact.telegram` = "Zahra_ejraei"
- `site.stats.projects` = "100+"
- `site.stats.experienceYears` = "3+"
- `site.stats.languages` = "3"
- `site.stats.satisfaction` = "98%"
- `site.social.instagram` (url placeholder)
- `site.social.telegram` (url placeholder)
- `site.social.whatsapp` (url placeholder)
- `site.social.linkedin` (url placeholder)
- `site.social.youtube` (url placeholder)

---

## 5. API Endpoints (Summary)

Public:
- `POST /api/contact`
- `POST /api/bookings`
- `POST /api/testimonials`
- `GET /api/posts`
- `GET /api/posts/[slug]`
- `GET /api/works`
- `GET /api/works/[slug]`

Admin:
- `GET /api/admin/bookings`
- `PATCH /api/admin/bookings/[id]`
- `GET/POST /api/admin/works`
- `PATCH/DELETE /api/admin/works/[id]`
- `GET/POST /api/admin/posts`
- `PATCH/DELETE /api/admin/posts/[id]`
- `GET/PATCH /api/admin/testimonials`
- `GET/PATCH /api/admin/contacts`
- `GET/PATCH /api/admin/settings`

Auth:
- `POST /api/auth/login`
- `POST /api/auth/logout`

All admin APIs must be protected by authentication and role checks.

---

## 6. Security Mindset (OWASP-Style Overview)

Rules:
1. Zod validation for all APIs
2. Prisma only (no raw SQL)
3. Secure auth (bcrypt/argon2, HttpOnly cookies)
4. Rate limiting at least on:
   - `/api/contact`, `/api/bookings`, `/api/testimonials`, `/api/auth/login`
5. Security headers (CSP, etc.)
6. No stack traces to client
7. File uploads: type/size validation, never execute uploads

Anti-spam requirement:
- Order form uses a math captcha
- Backend must verify captcha server-side

---

## 7. Design System & Themes (Strict Home Page Rules)

### 7.1 Theme Modes
- Light / Dark / Ultra-Dark must exist

### 7.2 Brand Palette (as implemented now)
- Accent: `#3B82F6`
- Secondary: `#8B5CF6`
- Support: `#22D3EE`

### 7.3 Spacing (MUST for readability)
- Services cards grid gap MUST be clearly visible:
  - `gap-8` on mobile for the “translation types” cards (or equivalent spacing)
  - card padding `p-6`
  - section spacing `mt-10` / `mt-12` where needed
- The goal: cards are **not crowded**, readable, and visually separated.

### 7.4 Selection behavior
- Service cards selectable:
  - Selected card gets glow + border highlight
  - Selecting a card updates “project type” dropdown in the form

---

## 8. Home Page Content Rules (fa) — MUST MATCH

### 8.1 Identity
- City: **شیراز**
- Replace “مترجم حرفه‌ای” with: **مترجم باتجربه** (or similar neutral/فاخر, non-claimy)

### 8.2 Languages (with rotating icon)
- فارسی ⇄ انگلیسی (rotating arrows icon)
- فارسی ⇄ فرانسوی (rotating arrows icon)

### 8.3 CTA and Contact
- Primary CTA: “شروع ارسال سفارش پروژه”
- Direct contact MUST be Telegram:
  - “تلگرام: @Zahra_ejraei”
- No phone number shown in footer.

### 8.4 Stats
- +100 پروژه موفق
- +3 سال سابقه
- 3 زبان تخصصی
- 98% رضایت مشتری

### 8.5 Services list
Must include:
- ترجمه پزشکی
- ترجمه سیاسی
- ترجمه حقوقی
- ترجمه ورزشی
- ترجمه علمی
- ترجمه آرایشی
- ترجمه کامپیوتر
- تمامی حوزه‌ها

### 8.6 Order Box
- Title: “باکس سفارش پروژه”
- Fields: name, phone, email (optional), project type, language pair, file (optional), details
- Math captcha required; submit disabled until correct
- No developer/security explanations in UI copy.

### 8.7 Social Icons
Show icons/labels for:
- Instagram
- Telegram
- WhatsApp
- LinkedIn
- YouTube

Links must be configurable later (via Settings keys above).

---

## 9. Supabase / Prisma / Environment Variables

Manual steps:
1. Create Supabase project
2. Copy Postgres connection string
3. Create `.env.local`:
```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/dbname"
```
Later add:
```env
NEXTAUTH_SECRET="some-random-long-string"
NEXTAUTH_URL="http://localhost:3000"
```

Prisma:
- `npx prisma init`
- `npx prisma migrate dev --name init`

---

## 10. AI Workflow

You will instruct the AI in phases:
- `RUN PHASE 6 NOW` (Next.js + TS + Tailwind + folder structure)
- `RUN PHASE 7 NOW` (Prisma + DB + auth + API hardening)
- Continue phases step-by-step.

AI must list files created/modified and any manual steps.
