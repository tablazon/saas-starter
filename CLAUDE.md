# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Commands

Development:
- `pnpm dev` - Start Next.js development server with Turbopack
- `pnpm build` - Build the application for production
- `pnpm start` - Start production server

Database:
- `pnpm db:setup` - Create .env file with database setup
- `pnpm db:migrate` - Run database migrations
- `pnpm db:seed` - Seed database with test user (test@test.com / admin123)
- `pnpm db:generate` - Generate Drizzle schema
- `pnpm db:studio` - Open Drizzle Studio for database management

Stripe (for local testing):
- `stripe login` - Log in to Stripe CLI
- `stripe listen --forward-to localhost:3000/api/stripe/webhook` - Listen for webhooks locally

## Architecture Overview

This is a Next.js 15 SaaS starter with:

**Authentication System:**
- JWT-based sessions stored in HTTP-only cookies
- Session management in `lib/auth/session.ts` with 24-hour expiry
- Global middleware in `middleware.ts` protects `/dashboard` routes
- bcrypt password hashing with 10 salt rounds

**Database Schema (Drizzle ORM + PostgreSQL):**
- **users**: Core user data with email/password auth, soft deletes
- **teams**: Multi-tenant structure with Stripe integration
- **teamMembers**: Many-to-many relationship with role-based access
- **activityLogs**: User action tracking with IP logging
- **invitations**: Team invitation system with status tracking

**Role-Based Access Control:**
- User roles: 'member' (default), 'owner'
- Team member roles: flexible string-based roles
- Activity logging for all user actions (see ActivityType enum)

**Stripe Integration:**
- Teams store `stripeCustomerId`, `stripeSubscriptionId`, `stripeProductId`
- Webhook handler at `/api/stripe/webhook` for subscription events
- Pricing page connects to Stripe Checkout

**App Structure:**
- `app/(dashboard)/` - Protected dashboard routes with shared layout
- `app/(login)/` - Public auth pages (sign-in, sign-up)
- `app/api/` - API routes for user/team management and Stripe
- Route groups provide layout isolation

**Key Libraries:**
- Next.js 15 with PPR, client segment cache, and node middleware
- Drizzle ORM for type-safe database operations
- SWR for client-side data fetching with fallback data from server
- shadcn/ui components with Radix primitives
- Zod for schema validation (implied by dependencies)

**Path Aliases:**
- `@/*` maps to project root for clean imports

**Default Test Credentials:**
- Email: test@test.com
- Password: admin123