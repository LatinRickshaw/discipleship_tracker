# Phase 1 Implementation Plan: Core Infrastructure

## Overview

Phase 1 establishes the foundational infrastructure for the Kingdom Discipleship Tracker MVP. This is a **1-week sprint** that creates authentication, database schema, and basic UI scaffolding. All subsequent phases depend on Phase 1 completion.

**Current State:** Greenfield project with comprehensive planning documentation but zero code implementation.

**Goal:** Enable user authentication, role selection (Disciple/Discipler), and basic protected routing.

---

## Epic Breakdown

### Epic 1: Project Initialization & Setup
**Priority:** BLOCKER (must complete first)
**Estimated Effort:** 2-3 hours
**Dependencies:** None

#### Tasks:

**1.1: Initialize Next.js Project**
- Create new Next.js 14 app with TypeScript and App Router
- Configure Tailwind CSS for styling
- Set up project folder structure:
  ```
  discipleship_tracker/
  ├── frontend/           (Next.js app)
  ├── supabase/          (DB schema & migrations)
  └── .env.local         (environment variables)
  ```
- Create `.gitignore` for node_modules, .env files, etc.
- Initialize package.json with dependencies:
  - `next`, `react`, `react-dom`
  - `@supabase/supabase-js`, `@supabase/auth-helpers-nextjs`
  - `tailwindcss`, `autoprefixer`, `postcss`

**1.2: Set Up Supabase Project**
- Create new Supabase project via [supabase.com](https://supabase.com) dashboard
- Note project URL and anon key for environment variables
- Create `.env.local` with:
  ```
  NEXT_PUBLIC_SUPABASE_URL=your_project_url
  NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
  ```
- Create `frontend/lib/supabaseClient.js` with Supabase client initialization
- Test connection by running a simple query in the Supabase dashboard

**1.3: Configure Project Structure**
- Create folder structure:
  ```
  frontend/
  ├── app/                    # Next.js 14 App Router
  │   ├── page.jsx           # Landing/login page
  │   ├── layout.jsx         # Root layout
  │   ├── onboarding/
  │   │   └── page.jsx       # Role selection
  │   └── dashboard/
  │       └── page.jsx       # Main dashboard
  ├── components/             # Reusable components
  ├── contexts/               # React contexts
  ├── lib/                    # Utilities and clients
  └── styles/                 # Global styles
  ```
- Create basic `layout.jsx` with metadata and global styles
- Set up TypeScript/JSX configurations

**Verification:**
- ✅ `npm run dev` starts Next.js app without errors
- ✅ Supabase client connects successfully
- ✅ Environment variables load correctly
- ✅ Tailwind CSS classes render properly

---

### Epic 2: Database Schema Design
**Priority:** BLOCKER (required for auth and all data operations)
**Estimated Effort:** 3-4 hours
**Dependencies:** Epic 1 complete

#### Tasks:

**2.1: Design Core Database Tables**
- Create `supabase/schema.sql` with three core tables:

  **Table 1: `users`**
  - Extends Supabase Auth `auth.users`
  - Fields:
    - `id` (UUID, primary key, references auth.users)
    - `email` (text, unique)
    - `role` (text, check constraint: 'disciple' or 'discipler')
    - `full_name` (text, nullable)
    - `created_at` (timestamp with time zone, default now())
    - `updated_at` (timestamp with time zone, default now())

  **Table 2: `check_ins`**
  - Daily reflections submitted by disciples
  - Fields:
    - `id` (UUID, primary key, default uuid_generate_v4())
    - `user_id` (UUID, foreign key to users.id)
    - `reflection_text` (text, for "crucible moment")
    - `pillar_scores` (jsonb, stores {inner_life: 3, new_humanity: 4, justice: 2, culture_making: 4})
    - `submitted_at` (timestamp with time zone, default now())
    - `analysis_unlocked` (boolean, default false) -- gated by discipler

  **Table 3: `relationships`**
  - Discipler-Disciple pairings
  - Fields:
    - `id` (UUID, primary key, default uuid_generate_v4())
    - `disciple_id` (UUID, foreign key to users.id)
    - `discipler_id` (UUID, foreign key to users.id)
    - `status` (text, check constraint: 'pending', 'active', 'inactive')
    - `created_at` (timestamp with time zone, default now())
    - `last_meeting_confirmed_at` (timestamp with time zone, nullable)

**2.2: Add Row-Level Security (RLS) Policies**
- Enable RLS on all three tables
- **Users table policies:**
  - Users can read their own profile
  - Users can update their own profile (role cannot be changed after onboarding)
- **Check-ins table policies:**
  - Disciples can create/read their own check-ins
  - Disciplers can read check-ins of their assigned disciples
  - No one can update check-ins after submission (immutable records)
- **Relationships table policies:**
  - Disciples can read relationships where they are the disciple
  - Disciplers can read relationships where they are the discipler
  - Disciples can create relationship requests
  - Disciplers can update relationship status (accept/reject)

**2.3: Create Database Migrations**
- Run schema.sql in Supabase SQL editor to create tables
- Verify tables appear in Supabase Table Editor
- Test RLS policies by attempting operations as different users
- Create seed data for testing (2 test users: 1 disciple, 1 discipler)

**Verification:**
- ✅ All three tables exist in Supabase dashboard
- ✅ RLS policies prevent unauthorized access
- ✅ Foreign key constraints enforce referential integrity
- ✅ Test users can be created with different roles

---

### Epic 3: Authentication System
**Priority:** BLOCKER (required for all protected pages)
**Estimated Effort:** 4-5 hours
**Dependencies:** Epic 1 & 2 complete

#### Tasks:

**3.1: Create Authentication Context**
- Build `frontend/contexts/AuthContext.jsx`:
  - Provides `useAuth()` hook for components
  - Manages authentication state (user, session, loading)
  - Handles sign up, sign in, sign out operations
  - Listens to Supabase `onAuthStateChange` events
  - Fetches user profile (including role) after authentication
- Wrap app with `AuthProvider` in `app/layout.jsx`

**3.2: Build Landing/Login Page**
- Create `app/page.jsx` as landing page:
  - Display app name and brief description
  - Email + password login form
  - "Sign Up" link to registration
  - Handle form submission with Supabase Auth
  - Show error messages for invalid credentials
  - Redirect to `/onboarding` after successful signup
  - Redirect to `/dashboard` if already logged in
- Use Tailwind CSS for clean, accessible form design

**3.3: Build Sign-Up Flow**
- Create `app/signup/page.jsx`:
  - Email + password registration form
  - Full name field (optional)
  - Password confirmation validation
  - Terms of service checkbox (placeholder for now)
  - Call `supabase.auth.signUp()` on submission
  - Redirect to `/onboarding` after successful registration
  - Handle error states (email already exists, weak password, etc.)

**3.4: Implement Protected Route Middleware**
- Create `frontend/lib/auth-helpers.js`:
  - `getServerSession()` - retrieves session server-side
  - `requireAuth()` - middleware to protect routes
- Add route protection to `/dashboard` and `/onboarding`
- Redirect unauthenticated users to login page
- Redirect authenticated users away from login page

**Verification:**
- ✅ User can sign up with email/password
- ✅ User can log in with credentials
- ✅ Invalid credentials show error message
- ✅ Authenticated users cannot access login page
- ✅ Unauthenticated users redirected from protected pages
- ✅ Session persists across page refreshes

---

### Epic 4: Onboarding & Role Selection
**Priority:** HIGH (required before users can access dashboard)
**Estimated Effort:** 3-4 hours
**Dependencies:** Epic 3 complete

#### Tasks:

**4.1: Create Onboarding Page**
- Build `app/onboarding/page.jsx`:
  - Welcome message explaining the app's purpose
  - Role selection UI with two options:
    - **"I am a Disciple"** - button with description
    - **"I am a Discipler"** - button with description
  - Brief explanation of each role (1-2 sentences)
  - Cannot proceed without selecting a role

**4.2: Implement Role Selection Logic**
- On role selection, update user record in `users` table:
  - Call API route `/api/user/update-role`
  - Update `role` field to 'disciple' or 'discipler'
  - Prevent role changes after initial selection (business rule)
- Redirect to `/dashboard` after role is saved
- Show loading state during save operation

**4.3: Add Role-Based Access Control**
- Create `frontend/lib/rbac.js` helper:
  - `isDisciple(user)` - checks if user has disciple role
  - `isDiscipler(user)` - checks if user has discipler role
  - `requireRole(user, role)` - throws error if role doesn't match
- Use RBAC helpers in protected pages to show role-specific UI

**Verification:**
- ✅ New users redirected to onboarding after signup
- ✅ Role selection updates database correctly
- ✅ Users with roles skip onboarding and go to dashboard
- ✅ Cannot change role after initial selection
- ✅ Role persists in database and session

---

### Epic 5: Basic Dashboard Shell
**Priority:** MEDIUM (provides landing page after auth, but no functionality yet)
**Estimated Effort:** 2-3 hours
**Dependencies:** Epic 3 & 4 complete

#### Tasks:

**5.1: Create Disciple Dashboard**
- Build `app/dashboard/disciple/page.jsx`:
  - Display welcome message with user's name
  - Show placeholder cards for:
    - "Daily Check-In" (will be implemented in Phase 2)
    - "My Growth Chart" (radar chart placeholder)
    - "Recent Reflections" (history placeholder)
  - Include sign-out button in header/navigation
  - Route protection: only accessible to users with 'disciple' role

**5.2: Create Discipler Dashboard**
- Build `app/dashboard/discipler/page.jsx`:
  - Display welcome message with user's name
  - Show placeholder list for:
    - "My Disciples" (will show assigned disciples in Phase 4)
    - "Pending Check-Ins" (will show unconfirmed meetings)
  - Include sign-out button in header/navigation
  - Route protection: only accessible to users with 'discipler' role

**5.3: Add Dashboard Navigation**
- Create `frontend/components/Navigation.jsx`:
  - Displays user name and role
  - Sign-out button
  - Logo/app name
  - Use Tailwind for responsive design
- Include Navigation in both dashboard layouts

**5.4: Implement Role-Based Dashboard Routing**
- Update `app/dashboard/page.jsx` to redirect based on role:
  - If user is disciple → redirect to `/dashboard/disciple`
  - If user is discipler → redirect to `/dashboard/discipler`
  - If no role set → redirect to `/onboarding`
- Ensure smooth user experience with loading states

**Verification:**
- ✅ Disciple users see disciple dashboard with placeholders
- ✅ Discipler users see discipler dashboard with placeholders
- ✅ Sign-out button works and redirects to login
- ✅ Navigation displays user info correctly
- ✅ Role-based routing works without errors

---

## Critical Files Summary

### Files to Create (Phase 1)

**Configuration & Setup:**
- `package.json` - Dependencies and scripts
- `.env.local` - Environment variables (Supabase keys)
- `tailwind.config.js` - Tailwind CSS configuration
- `tsconfig.json` or `jsconfig.json` - TypeScript/JavaScript config
- `.gitignore` - Ignore node_modules, .env, etc.

**Database:**
- `supabase/schema.sql` - Database tables and RLS policies

**Core Infrastructure:**
- `frontend/lib/supabaseClient.js` - Supabase client initialization
- `frontend/contexts/AuthContext.jsx` - Authentication state management
- `frontend/lib/auth-helpers.js` - Server-side auth utilities
- `frontend/lib/rbac.js` - Role-based access control helpers

**Pages (Next.js App Router):**
- `frontend/app/layout.jsx` - Root layout with AuthProvider
- `frontend/app/page.jsx` - Landing/login page
- `frontend/app/signup/page.jsx` - Registration page
- `frontend/app/onboarding/page.jsx` - Role selection page
- `frontend/app/dashboard/page.jsx` - Dashboard router (redirects by role)
- `frontend/app/dashboard/disciple/page.jsx` - Disciple dashboard
- `frontend/app/dashboard/discipler/page.jsx` - Discipler dashboard

**Components:**
- `frontend/components/Navigation.jsx` - Dashboard navigation/header

**API Routes (if needed):**
- `frontend/app/api/user/update-role/route.js` - Update user role endpoint

---

## Dependencies & Blockers

### Critical Path
1. **Epic 1 (Setup) blocks everything** - must complete first
2. **Epic 2 (Database) blocks Epic 3, 4, 5** - auth needs tables
3. **Epic 3 (Auth) blocks Epic 4, 5** - onboarding/dashboard need auth
4. **Epic 4 (Onboarding) blocks Epic 5** - dashboard needs role selection

### External Dependencies
- Supabase account and project created
- Email/password auth enabled in Supabase dashboard
- Internet connection for API calls

### Assumptions
- Using Next.js 14 with App Router (not Pages Router)
- Using JavaScript (JSX) - can switch to TypeScript if preferred
- Desktop-first design (mobile optimization deferred to post-MVP)
- Simple email/password auth (no OAuth providers in Phase 1)

---

## Verification Checklist (End of Phase 1)

### Must Pass All:
- ✅ User can sign up with email/password
- ✅ User can log in with valid credentials
- ✅ User selects role (Disciple or Discipler) during onboarding
- ✅ Login redirects to appropriate dashboard based on role
- ✅ Database tables created and tested (Users, CheckIns, Relationships)
- ✅ RLS policies prevent unauthorized data access
- ✅ Protected pages redirect unauthenticated users to login
- ✅ Sign-out button works and clears session
- ✅ No console errors or warnings during happy path flow
- ✅ App runs locally with `npm run dev`

### Testing Procedure:
1. Start fresh: clear Supabase auth users and database tables
2. Sign up as new user (User A)
3. Select "Disciple" role in onboarding
4. Verify redirect to disciple dashboard
5. Sign out
6. Sign up as another user (User B)
7. Select "Discipler" role
8. Verify redirect to discipler dashboard
9. Sign out User B
10. Log in as User A again
11. Verify session restored and correct dashboard shown
12. Attempt to access `/dashboard/discipler` as User A
13. Verify access denied or redirect (RBAC test)

---

## Estimated Timeline

**Total Phase 1 Effort:** ~15-20 hours
**Recommended Schedule (1 week):**
- **Day 1-2:** Epics 1 & 2 (Setup + Database) - 5-7 hours
- **Day 3-4:** Epic 3 (Authentication) - 4-5 hours
- **Day 5:** Epic 4 (Onboarding) - 3-4 hours
- **Day 6:** Epic 5 (Dashboard Shell) - 2-3 hours
- **Day 7:** Testing, bug fixes, polish - 2-3 hours

**Assumes:** 3-4 hours/weekday + weekend work (per implementation plan)

---

## Phase 1 Completion Criteria

Phase 1 is **DONE** when:
1. All verification checklist items pass
2. No critical bugs blocking user flow
3. Database schema matches specification
4. Authentication works reliably
5. Role-based routing functions correctly
6. Code is committed to git repository
7. `.env.local.example` file created (without secrets)
8. Basic README updated with setup instructions

**Phase 1 BLOCKS Phase 2** - Cannot proceed to check-in forms and radar charts until authentication and database are stable.

---

## Notes for Implementation

### Tech Stack Decisions (Phase 1)
- **Next.js 14 App Router** (not Pages Router) - modern, better DX
- **Supabase Auth + Database** - all-in-one solution for speed
- **Tailwind CSS** - rapid styling without custom CSS files
- **JavaScript (JSX)** - can migrate to TypeScript later if needed
- **PostgreSQL via Supabase** - free tier sufficient for alpha testing

### Anti-Patterns to Avoid
- Don't build custom auth (use Supabase Auth primitives)
- Don't over-engineer role system (only 2 roles needed)
- Don't add features not in Phase 1 scope (e.g., profile editing, password reset)
- Don't optimize prematurely (focus on working code first)

### Quick Wins
- Use Supabase UI library for rapid form building (optional)
- Copy-paste Tailwind components for speed
- Use Supabase Table Editor to visually inspect data during testing
- Leverage Supabase Auth Helpers for Next.js (reduces boilerplate)

---

## Next Phase Preview

**Phase 2 (Week 2):** Disciple Core Loop
- Daily check-in form (text reflection)
- Four-pillar questionnaire (1-5 scales)
- Radar chart visualization (Chart.js)
- Check-in history view

**Depends on:** Phase 1 complete + Darren beginning theological markdown (parallel work)
