# Technical Stack Documentation

**Project:** VolleyStack - Volleyball Club SaaS Platform  
**Architecture:** Multi-Tenant Headless CMS  
**Document Version:** 1.0  
**Last Updated:** January 2025

---

## Table of Contents

1. [Stack Overview](#stack-overview)
2. [Core Technology Decisions](#core-technology-decisions)
3. [Infrastructure Layer](#infrastructure-layer)
4. [Backend Layer](#backend-layer)
5. [Frontend Layer](#frontend-layer)
6. [Data Layer](#data-layer)
7. [Integration Layer](#integration-layer)
8. [DevOps & Deployment](#devops--deployment)
9. [Monitoring & Observability](#monitoring--observability)
10. [Security Stack](#security-stack)
11. [Cost Analysis](#cost-analysis)
12. [Technology Alternatives Considered](#technology-alternatives-considered)

---

## Stack Overview

### Architecture Type
**Multi-Tenant SaaS with Headless CMS**
- Single codebase serves all tenants (volleyball clubs)
- Shared infrastructure with logical data isolation
- Unified deployment, distributed edge delivery

### Stack Philosophy
- **Developer Experience First:** TypeScript throughout, strong typing, excellent tooling
- **Modern & Proven:** Latest stable versions of battle-tested technologies
- **Open Source Preferred:** Avoid vendor lock-in, maintain full control
- **Serverless-Ready:** Start serverless (Vercel), scale to containers if needed
- **Cost-Conscious:** Leverage free tiers initially, optimize costs at scale

### Technology Summary

```
┌─────────────────────────────────────────────────────────┐
│                     USER BROWSER                        │
├─────────────────────────────────────────────────────────┤
│  Next.js 15 Frontend (SSR/SSG)                         │
│  React 19 + TypeScript                                  │
│  Tailwind CSS + shadcn/ui Components                    │
└─────────────────┬───────────────────────────────────────┘
                  │ HTTPS
┌─────────────────▼───────────────────────────────────────┐
│  VERCEL EDGE NETWORK (CDN + Serverless Functions)      │
└─────────────────┬───────────────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────────────┐
│  Payload CMS 3.0 (Backend + Admin Panel)               │
│  - API Layer (REST/GraphQL)                            │
│  - Auth & Access Control                               │
│  - Jobs Queue (Background Tasks)                        │
│  - Multi-Tenant Plugin                                  │
│  - Stripe Plugin                                        │
└─────────────────┬───────────────────────────────────────┘
                  │
    ┌─────────────┼─────────────┬──────────────┐
    │             │             │              │
┌───▼────┐  ┌────▼─────┐  ┌───▼─────┐  ┌────▼────┐
│ Neon   │  │  Vercel  │  │ Resend  │  │ Stripe  │
│ PostgreSQL │  Blob   │  │  Email  │  │Payments │
└────────┘  │ Storage  │  └─────────┘  └─────────┘
            └──────────┘
    
┌──────────────────────────────────────────────────────────┐
│  EXTERNAL INTEGRATIONS                                   │
│  - League APIs (schedules, standings)                   │
│  - Instagram Basic Display API                          │
│  - YouTube Data API v3                                  │
│  - n8n (workflow automation)                            │
└──────────────────────────────────────────────────────────┘
```

---

## Core Technology Decisions

### 1. Payload CMS 3.0

**Decision:** Use Payload CMS as the headless CMS and backend framework

**Rationale:**
- **Native Next.js Integration:** Payload 3.0 installs directly into Next.js `/app` folder, eliminating separate backend deployment
- **Official Multi-Tenant Plugin:** Purpose-built for SaaS applications, handles tenant isolation via access control
- **Built-in Stripe Plugin:** Two-way sync between Payload and Stripe for subscription management
- **Jobs Queue:** Background task processing (API polling, email sending) without external job queue infrastructure
- **TypeScript-First:** Entire configuration is code, fully type-safe
- **Local API:** Query database directly on server without HTTP overhead
- **MIT License:** Fully open source, no revenue restrictions

**Version:** 3.0+ (stable as of Q4 2024)

**Alternatives Considered:**
- Directus: Better UI, but requires separate deployment (Vue.js based, not Next.js native)
- Strapi: Lacks native multi-tenancy support, would require significant custom development
- KeystoneJS: Good but smaller community, less mature ecosystem

---

### 2. Next.js 15 (App Router)

**Decision:** Use Next.js 15 with App Router for frontend and backend

**Rationale:**
- **Unified Stack:** Payload installs directly into Next.js, single deployment
- **Server Components:** Reduce JavaScript bundle size, improve performance
- **Hybrid Rendering:** SSR for dynamic content, SSG for static pages (rosters, schedules cache well)
- **API Routes:** Handle webhooks and custom backend logic
- **Image Optimization:** Built-in next/image for responsive images
- **Edge Runtime:** Deploy serverless functions globally for low latency
- **Industry Standard:** Largest React framework, excellent documentation and tooling

**Version:** 15.x (latest stable)

**Key Features Used:**
- App Router (not Pages Router)
- Server Components and Server Actions
- Route Groups for organization
- Middleware for tenant resolution
- API Routes for webhooks
- Edge Runtime for performance-critical routes

---

### 3. PostgreSQL (via Neon)

**Decision:** Use PostgreSQL as primary database, hosted on Neon

**Rationale:**
- **Relational Data Model:** Volleyball data (teams → players, schedules → games) is inherently relational
- **Multi-Tenant Schemas:** Separate PostgreSQL schema per tenant provides strong isolation
- **ACID Compliance:** Data integrity critical for schedules, rosters, payments
- **Query Performance:** Superior for joins (team rosters with player details)
- **Drizzle ORM:** Payload 3.0 uses Drizzle, excellent PostgreSQL support with type safety
- **Migrations:** Built-in migration support (Payload + Drizzle)

**Why Neon:**
- **Serverless PostgreSQL:** Autoscaling, scales to zero on free tier
- **Branching:** Git-like database branches for testing
- **Generous Free Tier:** 3GB storage, 100 hours compute/month free
- **Edge Caching:** Query caching at edge locations
- **Cost-Effective:** $20-50/month for 50 clubs, vs. $100+ for traditional RDS

**Version:** PostgreSQL 16.x

**Alternatives Considered:**
- MongoDB: Originally considered but PostgreSQL better suits relational sports data
- Vercel Postgres: More expensive than Neon at scale
- AWS RDS: More expensive, requires more DevOps overhead

---

### 4. TypeScript

**Decision:** TypeScript throughout entire stack (backend + frontend)

**Rationale:**
- **Type Safety:** Catch errors at compile time, especially critical for multi-tenant data isolation
- **Better DX:** Autocomplete, refactoring support, documentation in types
- **Payload Native:** Payload config is TypeScript, generates types from collections
- **Industry Standard:** Expected in modern web development

**Version:** 5.x (latest stable)

**Configuration:**
- Strict mode enabled
- Path aliases configured (`@/components`, `@/lib`)
- Payload auto-generates types from collections

---

## Infrastructure Layer

### Hosting: Vercel

**Decision:** Deploy to Vercel (initially), with migration path to Railway/AWS

**Rationale:**
- **Zero Configuration:** Push to GitHub → automatic deployment
- **Serverless Functions:** Automatic scaling, pay-per-use
- **Edge Network:** Global CDN for static assets and edge functions
- **Preview Deployments:** Every PR gets preview URL for testing
- **Next.js Optimized:** Built by Next.js creators, best-in-class support
- **Free Tier:** Sufficient for development and first 10-15 clubs

**Pricing Tiers:**
- **Hobby (Free):** Development + pilot testing (5 clubs)
- **Pro ($20/month):** Production with 25-100 clubs
- **Enterprise:** Consider Railway/AWS migration beyond 100 clubs

**Migration Path:**
Since Payload + Next.js are fully portable (not Vercel-specific), migration to Railway or AWS is straightforward if needed at scale.

---

### File Storage: Vercel Blob Storage

**Decision:** Use Vercel Blob Storage initially, Cloudinary for advanced optimization

**Rationale:**
- **Zero Configuration:** Integrated with Vercel deployment
- **Edge Network:** Fast global delivery via Cloudflare
- **Simple Pricing:** $0.15/GB storage + $0.25/GB bandwidth
- **Generous Free Tier:** First 100GB bandwidth free monthly

**Future Enhancement:** Cloudinary
- Automatic image resizing and format conversion
- Responsive images (srcset generation)
- Cost: Free tier (25GB), then $89/month for 135GB
- Add when image optimization becomes bottleneck (50+ clubs)

**Alternatives Considered:**
- AWS S3: Cheaper at scale ($0.023/GB storage) but requires more setup
- Cloudflare R2: Zero egress fees, but less integrated with Next.js

---

### CDN: Vercel Edge Network (Cloudflare)

**Decision:** Leverage Vercel's built-in CDN (powered by Cloudflare)

**Rationale:**
- **Global Coverage:** 100+ edge locations worldwide
- **Automatic:** No configuration needed
- **Smart Caching:** Understands Next.js ISR (Incremental Static Regeneration)
- **Included:** No additional cost beyond Vercel hosting

**Cache Strategy:**
- Static assets (images, CSS, JS): Cache for 1 year with immutable filenames
- Club pages: ISR with 5-minute revalidation
- Schedule pages: ISR with 15-minute revalidation
- API responses: Cache-Control headers for appropriate TTLs

---

## Backend Layer

### Payload CMS Collections Architecture

**Platform-Level Collections (No Tenant Scoping):**

1. **Tenants**
   - Club metadata (name, slug, domain)
   - Stripe customer ID and subscription status
   - Theme configuration (colors, logo)
   - Created/updated timestamps

2. **Users**
   - Email, password hash
   - Global role (super_admin, platform_viewer)
   - Tenant associations with per-tenant roles
   - Authentication tokens

**Tenant-Scoped Collections (Multi-Tenant Plugin Applied):**

3. **Teams**
   - Name, age group, season
   - Coach relationships
   - Team photo/logo
   - Active/inactive status

4. **Players**
   - First name, last name
   - Jersey number, position
   - Team relationship (foreign key)
   - Player photo, bio
   - Contact information (optional)

5. **Schedules**
   - Game date/time
   - Opponent name
   - Home/away indicator
   - Location/venue
   - Result (win/loss/scheduled)
   - Score details
   - Team relationship (foreign key)
   - External league API ID for syncing

6. **Posts**
   - Title, slug
   - Rich text content (Lexical editor)
   - Author (user relationship)
   - Published date
   - Featured image
   - Categories/tags
   - Draft/published status

7. **Media**
   - File storage (Vercel Blob)
   - Alt text for accessibility
   - Metadata (dimensions, file size)
   - Upload date and user

8. **Pages** (Optional - for custom club pages)
   - Flexible page builder
   - Blocks (hero, content, gallery, etc.)
   - SEO metadata

---

### Access Control

**Payload's Multi-Tenant Access Control Pattern:**

```typescript
// Tenant-scoped collections automatically filtered by tenant
// Example: Players collection

export const Players: CollectionConfig = {
  slug: 'players',
  access: {
    // Super admins see all tenants, club admins only their tenant
    read: ({ req: { user } }) => {
      if (user.role === 'super_admin') return true
      
      return {
        tenant: {
          equals: user.lastLoggedInTenant // Set by multi-tenant plugin
        }
      }
    },
    
    // Only club admins and coaches can create players
    create: ({ req: { user } }) => {
      return ['super_admin', 'club_admin', 'coach'].includes(user.role)
    },
    
    // Users can only update players in their tenant
    update: ({ req: { user } }) => {
      if (user.role === 'super_admin') return true
      
      return {
        tenant: {
          equals: user.lastLoggedInTenant
        }
      }
    }
  }
}
```

**Role Hierarchy:**
1. **Super Admin:** Platform owner, access to all tenants and data
2. **Club Admin:** Full control within their assigned tenant(s)
3. **Coach:** Read all, create/update team-related content within their tenant
4. **Viewer:** Read-only access to their tenant (future role)

---

### Background Jobs (Payload Jobs Queue)

**Job Types:**

1. **League Data Sync (Scheduled)**
   - Frequency: Nightly at 2:00 AM UTC
   - Fetches schedules, standings, results from league API
   - Updates Schedule collection with latest data
   - Preserves historical data when teams are unlinked
   - Error handling: Retry 3x with exponential backoff, alert on failure
   - Rationale: League APIs update weekly (standings Sunday night, fixtures Monday night), 
     so nightly sync is sufficient and reduces API load

2. **Social Media Sync (Scheduled)**
   - Frequency: Every 60 minutes
   - Fetches Instagram posts and YouTube videos
   - Stores in Media collection with source metadata
   - Rate limit management: Track API usage, throttle if approaching limits

3. **Email Digest (Scheduled)**
   - Frequency: Weekly (configurable per club)
   - Aggregates recent posts, upcoming games
   - Sends via Resend to club's member list
   - Personalized per club using tenant context

4. **Backup Verification (Scheduled)**
   - Frequency: Daily at 2am UTC
   - Verifies Neon automatic backups completed
   - Tests restore process monthly
   - Alerts on failure

5. **Analytics Aggregation (Scheduled)**
   - Frequency: Daily at 1am UTC
   - Aggregates page views, popular content
   - Updates club dashboard metrics
   - Cleans up old analytics data (>90 days)

**Job Configuration:**
```typescript
// payload.config.ts
export default buildConfig({
  jobs: {
    tasks: [
      {
        slug: 'sync-league-data',
        handler: async ({ job, payload }) => {
          // Fetch all active tenants
          const tenants = await payload.find({
            collection: 'tenants',
            where: { subscriptionStatus: { equals: 'active' }}
          })
          
          for (const tenant of tenants.docs) {
            // Sync data for each tenant
            await syncLeagueDataForTenant(tenant.id, payload)
          }
        },
        schedule: '*/15 * * * *' // Every 15 minutes
      }
    ]
  }
})
```

---

## Frontend Layer

### React + TypeScript

**Version:** React 19 (Next.js 15 includes React 19)

**Key Patterns:**
- Server Components by default (async components, no hydration overhead)
- Client Components only when needed (interactivity, browser APIs)
- Server Actions for form submissions and mutations
- Suspense boundaries for loading states

---

### UI Component Library: shadcn/ui

**Decision:** Use shadcn/ui component collection with Radix UI primitives

**Rationale:**
- **Copy-Paste, Not NPM:** Components copied into codebase, full ownership and customization
- **Accessibility:** Built on Radix UI primitives (WCAG 2.1 compliant)
- **Tailwind Integration:** Designed for Tailwind CSS styling
- **High Quality:** Production-ready components used by Vercel, Cal.com, others
- **No Runtime Overhead:** Components are just React + Tailwind, no library bundle

**Components Used:**
- Form components (input, textarea, select, checkbox, radio)
- Layout (card, dialog, sheet, tabs)
- Data display (table, badge, avatar)
- Feedback (alert, toast, skeleton)
- Navigation (dropdown menu, command palette)

**Installation Pattern:**
```bash
npx shadcn-ui@latest add button
npx shadcn-ui@latest add form
npx shadcn-ui@latest add card
# Components added to /components/ui/
```

---

### Styling: Tailwind CSS

**Version:** 3.4.x

**Configuration:**
- Custom color palette matching volleyball/sports theme
- Extended spacing scale for consistent layout
- Custom breakpoints if needed (default: sm, md, lg, xl, 2xl)
- Typography plugin for rich text content
- Forms plugin for styled form elements

**Design Tokens:**
```javascript
// tailwind.config.ts
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: { /* Club's primary color - customizable per tenant */ },
        secondary: { /* Club's secondary color */ },
        brand: { /* Platform brand colors */ }
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        display: ['Poppins', 'system-ui', 'sans-serif']
      }
    }
  }
}
```

**Per-Tenant Theming:**
- Tenant-specific CSS custom properties set via inline style tag
- Tailwind utilities reference CSS variables
- Example: `bg-primary-500` → `var(--color-primary-500)` → tenant's chosen color

---

### Form Handling: React Hook Form + Zod

**React Hook Form:** Form state management
- Uncontrolled components for performance
- Built-in validation
- Excellent TypeScript support

**Zod:** Schema validation
- Type-safe validation schemas
- Shared between frontend and backend
- Generated error messages

## Data Layer

**Platform-level:**
- `league-divisions` - Division metadata and API URLs
- `sub-leagues` - Pools/groups for nested divisions
- `external-teams` - Teams from standings API
- `team-league-associations` - Maps internal teams to external league teams
  - Stores sync preferences (schedules enabled, standings enabled)
  - Preserves historical data when unlinked
  - Allows re-linking with different external team

**Tenant-level:**
- `teams` - Internal team records
  - Can exist without league association (custom/development teams)
  - Warning flag if not linked: "Auto-sync disabled for this team"
- `league-standings-snapshots` - Historical tracking
- `match-fixtures` - Synced fixtures (preserved even if team unlinked)
- `players` - Team roster
  - Photos uploaded separately from CSV import
  - Email and phone optional fields

### Database: PostgreSQL via Neon

**Schema Organization:**

**Platform Schema (public):**
- `tenants` table
- `users` table
- `user_tenants` junction table
- `_payload_migrations` table

**Tenant Schemas (one per club):**
- `club_abc_123.teams`
- `club_abc_123.players`
- `club_abc_123.schedules`
- `club_abc_123.posts`
- `club_abc_123.media`
- `club_abc_123.pages`


**Indexing Strategy:**
- Primary keys: UUID with index
- Foreign keys: Indexed automatically
- Tenant ID: Composite index with other commonly queried fields
- Full-text search: GIN index on post content
- Date fields: Index on schedules.gameDate for calendar queries

---

### ORM: Drizzle

**Decision:** Drizzle ORM (Payload 3.0 uses Drizzle internally)

**Rationale:**
- **TypeScript-First:** Schema defined in TypeScript, generates types
- **SQL-Like API:** Familiar to SQL developers, low learning curve
- **Lightweight:** No runtime overhead, compiles to SQL
- **Migration Support:** Built-in migration generation and runner
- **PostgreSQL Optimized:** Takes advantage of PostgreSQL features


### Caching Strategy

**Multi-Layer Caching:**

**L1: Browser Cache**
- Static assets: 1 year with immutable filenames
- Service worker for offline support (future enhancement)

**L2: CDN Edge Cache (Vercel)**
- Static pages: Cache until ISR revalidation (5-15 minutes)
- API responses: Cache-Control headers, varies by endpoint
- Stale-while-revalidate for better UX

**L3: Application Cache (Next.js Data Cache)**
- React Server Component cache
- Fetch requests cached automatically with revalidation tags

**L4: Database Query Cache (Neon)**
- Automatic query result caching at edge locations
- Reduces round-trip time to database

**L5: In-Memory Cache (Optional - Redis if needed)**
- Not implemented initially
- Add if frequent database queries become bottleneck
- Use for: session storage, rate limiting counters, job queue

**Cache Invalidation:**
- Time-based: Most content uses TTL (5-60 minutes)
- Event-based: Explicit revalidation on content updates via Next.js `revalidatePath()`
- Tag-based: Next.js cache tags for granular invalidation


## Integration Layer

### CSV Roster Import

**CSV Template Provided:**
- Clubs download pre-populated template with their exact team names
- Prevents team name mismatch errors
- Required fields: First Name, Last Name, Team Name
- Optional fields: Jersey Number, Position, Email, Phone

**Import Behavior:**
- Validates all rows before import (reject entire file if any errors)
- Skips duplicate players (matched by First Name + Last Name + Team)
- Shows warning: "5 players skipped (already exist)"
- Photo URLs included in CSV → Downloaded and stored in Vercel Blob Storage
- Photos can also be uploaded separately via player edit screen

**Error Handling:**
- Team name must match exactly (CSV template ensures this)
- Invalid positions rejected (must be: Setter, Outside Hitter, Middle Blocker, Libero, Opposite)
- Invalid jersey numbers rejected (must be 0-99)
- Returns detailed error report for admin to fix and re-upload

### Custom Domain Setup (Pro Tier Only)

**DNS Configuration Required:**
1. A record: @ → 76.76.21.21 (Vercel)
2. CNAME: www → cname.vercel-dns.com
3. TXT record: _vercel → [unique verification token] (for domain ownership verification)

**Verification Process:**
- Admin enters custom domain in settings
- System generates unique TXT verification code
- Admin adds TXT record to DNS
- Admin clicks "Verify Domain"
- System checks for TXT record presence
- Once verified, A record and CNAME checked
- SSL certificate provisioned automatically (24-48 hours)
- Custom domain remains inactive until SSL ready
- Status shown in admin panel: "SSL provisioning in progress (up to 48 hours)"

**Support Model:**
- Pro tier includes 24-hour support SLA
- Documentation provided (DNS setup guide with screenshots)
- Phone support available if club needs assistance

### Payment Processing: Stripe

**Implementation:** Payload Stripe Plugin

**Stripe Products:**
- Stripe Subscriptions (recurring billing)
- Stripe Customer Portal (self-service billing management)
- Stripe Webhooks (lifecycle events)

**Subscription Tiers:**
- **Starter Plan:** `price_starter_monthly` ($79/month)
- **Pro Plan:** `price_pro_monthly` ($149/month)

**Webhook Events Handled:**
- `customer.subscription.created`: Auto-provision club site
- `customer.subscription.updated`: Update subscription status
- `customer.subscription.deleted`: Deactivate club site (with grace period)
- `invoice.payment_succeeded`: Confirm payment, extend access
- `invoice.payment_failed`: Send payment failure email, restrict access after 3 failures

**Payload Stripe Plugin Configuration:**

### Email: Resend

**Decision:** Use Resend for transactional and marketing emails

**Rationale:**
- **Next.js Native:** Built by Vercel team, designed for Next.js
- **React Email:** Design emails with React components
- **Developer-Friendly:** Clean API, excellent TypeScript support
- **Deliverability:** High inbox placement rates
- **Affordable:** Free 3,000 emails/month, then $20/month for 50,000

**Email Types:**
1. **Transactional (via Payload hooks)**
   - Welcome email (on user creation)
   - Password reset (on reset request)
   - Payment confirmation (on successful payment)
   - Subscription status changes

2. **Marketing (via n8n workflows)**
   - Weekly newsletter digest
   - New feature announcements
   - Onboarding sequence (Days 1, 3, 7, 30)


---

### External APIs

**League/Governing Body API**
- **Purpose:** Fetch schedules, standings, results
- **Authentication:** API key or OAuth (depends on provider)
- **Rate Limits:** Varies by provider (typically 100-1000 requests/hour)
- **Error Handling:** Cache last successful response, fall back to cached data on failure

**Phyllo API**
- **Unified Social Media API:** Single integration for Instagram, YouTube, TikTok, Twitter
- **Pricing:** $50-100/month
- **Benefit:** Simplifies OAuth flows, normalizes data formats, manages rate limits
- **Decision:** Evaluate after validating demand for social feeds

---

## DevOps & Deployment

### Version Control: GitHub

**Repository Structure:**
```
volleyball-saas/
├── .github/
│   └── workflows/
│       ├── ci.yml (run tests on PR)
│       └── deploy.yml (auto-deploy main to production)
├── src/
├── public/
├── tests/
├── .env.example
├── README.md
└── package.json
```

**Branching Strategy:**
- `main`: Production (auto-deploys to Vercel production)
- `develop`: Staging (auto-deploys to Vercel preview)
- Feature branches: `feature/player-roster`, `fix/schedule-bug`

**PR Process:**
- All changes via pull request
- Require passing CI (tests, linting)
- Vercel preview deployment automatically created
- Manual approval required for merge to main

---

### CI/CD: Vercel + GitHub Actions

**Vercel Automatic Deployments:**
- Every commit to `main` → Production deployment
- Every pull request → Preview deployment with unique URL
- Environment variables managed in Vercel dashboard
- Automatic rollback on failed builds

**GitHub Actions for Testing:**

**Migration Strategy:**
- Drizzle migrations run automatically on build
- Database migrations applied before deployment
- Rollback plan: Revert Git commit, redeploy previous version

---

### Environment Variables

**Required Environment Variables:**

**Database:**
- `DATABASE_URI`: PostgreSQL connection string (Neon)
- `POSTGRES_SSL`: Enable SSL for production

**Payload:**
- `PAYLOAD_SECRET`: Encryption key for sessions (32+ character random string)
- `NEXT_PUBLIC_SERVER_URL`: Public URL (e.g., https://volleystack.com)

**Stripe:**
- `STRIPE_SECRET_KEY`: Stripe API secret key
- `STRIPE_WEBHOOK_SECRET`: Webhook signing secret
- `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`: Stripe public key (client-side)

**Email:**
- `RESEND_API_KEY`: Resend API key

**File Storage:**
- `BLOB_READ_WRITE_TOKEN`: Vercel Blob storage token (auto-set by Vercel)

**Optional:**
- `LEAGUE_API_KEY`: League data API key
- `INSTAGRAM_APP_SECRET`: Instagram OAuth app secret
- `YOUTUBE_API_KEY`: YouTube Data API key
- `N8N_WEBHOOK_SECRET`: Verify n8n webhook requests
- `SENTRY_DSN`: Sentry error tracking DSN

---

### Monitoring & Logging

**Application Monitoring: Sentry**
- **Error Tracking:** Catch and report runtime errors
- **Performance Monitoring:** Track slow database queries, API calls
- **Release Tracking:** Associate errors with Git commits
- **User Context:** Include tenant ID, user ID in error reports
- **Pricing:** Free tier (5,000 errors/month), then $26/month

**Log Aggregation: Vercel Logs**
- **Console Logs:** Captured automatically from serverless functions
- **Retention:** 1-day on Hobby, 7-day on Pro
- **Upgrade Path:** Export to external log service (Datadog, Logtail) if needed

**Uptime Monitoring: UptimeRobot**
- **HTTP Checks:** Ping homepage, API endpoint every 5 minutes
- **Status Page:** Public status page for transparency
- **Alerts:** Email/SMS when downtime detected
- **Pricing:** Free for up to 50 monitors

---

## Security Stack

### Authentication & Authorization

**Payload Built-in Auth:**
- JWT tokens with configurable expiration (7 days default)
- HTTP-only secure cookies
- Password hashing with bcrypt (cost factor: 10)
- Rate limiting on login attempts (5 attempts per 15 minutes)

**Password Requirements:**
- Minimum 8 characters
- Must include uppercase, lowercase, number
- Prevent common passwords (dictionary check)

**Session Management:**
- Refresh token rotation
- Automatic logout after inactivity (7 days)
- Logout on password change

---

### Data Security

**Encryption at Rest:**
- Neon PostgreSQL: AES-256 encryption enabled by default
- Vercel Blob Storage: Encrypted at rest

**Encryption in Transit:**
- HTTPS/TLS 1.3 for all traffic
- HSTS headers (force HTTPS)
- SSL certificates via Vercel (Let's Encrypt, auto-renewed)

**Secrets Management:**
- Environment variables stored in Vercel (encrypted)
- No secrets in Git repository
- API keys rotated quarterly

**Data Isolation:**
- PostgreSQL schemas per tenant
- Row-level security (RLS) policies as backup
- Access control verified at application and database level

---

### Application Security

**Input Validation:**
- Zod schemas on all user input
- Sanitize HTML content (XSS prevention)
- SQL injection prevented by parameterized queries (Drizzle ORM)

**CSRF Protection:**
- CSRF tokens on all state-changing requests
- SameSite cookie attribute (Strict)

**Rate Limiting:**
- API rate limits per tenant (1000 requests/hour)
- Auth endpoint rate limits (5 attempts/15 minutes)
- Implemented via Vercel Edge Config or Upstash Redis

**Security Headers:**
```typescript
// next.config.js
module.exports = {
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          { key: 'X-Frame-Options', value: 'DENY' },
          { key: 'X-Content-Type-Options', value: 'nosniff' },
          { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
          { key: 'Permissions-Policy', value: 'camera=(), microphone=(), geolocation=()' }
        ]
      }
    ]
  }
}
```

---

### Vulnerability Management

**Dependency Scanning:**
- Dependabot alerts enabled (GitHub)
- Weekly security updates check
- Automated PR for critical security patches

**Penetration Testing:**
- Annual third-party security audit (at 100+ clubs)
- Quarterly internal security review

**Compliance:**
- GDPR-ready (data export, deletion, privacy policy)
- PCI DSS Level 1 (via Stripe, no card data stored)

---

## Cost Analysis

### Infrastructure Costs by Scale

**Development (0-5 clubs):**
- Vercel: $0 (Hobby tier)
- Neon: $0 (Free tier: 3GB, 100 hours compute)
- Resend: $0 (Free tier: 3,000 emails/month)
- Sentry: $0 (Free tier: 5,000 errors/month)
- n8n: $20/month
- **Total: $20/month**

**Early Production (25 clubs):**
- Vercel Pro: $20/month
- Neon Scale: $20/month (3GB storage, 300 hours compute)
- Vercel Blob: $5/month (~30GB)
- Resend: $20/month (10,000 emails)
- Sentry: $26/month
- n8n: $20/month
- **Total: $111/month** ($4.44 per club)

**Production (50 clubs):**
- Vercel Pro: $20/month
- Neon Scale: $50/month (10GB storage, 750 hours compute)
- Vercel Blob: $10/month (~60GB)
- Resend: $20/month (10,000 emails)
- Sentry: $26/month
- n8n: $20/month
- UptimeRobot: $0 (free tier)
- **Total: $146/month** ($2.92 per club)

**Growth (100 clubs):**
- Vercel Pro: $20/month (consider Railway migration)
- Neon Business: $100/month (20GB storage, 1500 hours)
- Cloudinary: $89/month (image optimization)
- Resend: $50/month (50,000 emails)
- Sentry: $26/month
- n8n: $50/month (25,000 executions)
- **Total: $335/month** ($3.35 per club)

**Scale (200+ clubs) - Migration Recommended:**
- Railway or AWS ECS: $300/month (container hosting)
- AWS RDS PostgreSQL: $150/month
- Cloudinary: $89/month
- Resend: $80/month
- Sentry: $80/month
- n8n self-hosted: $50/month (VPS)
- **Total: $749/month** ($3.75 per club at 200)

### Personnel Costs (Future)

**Support Model:**
- **Pilot Phase (Month 4-6):** Manual support via email & phone
  - Response SLA: 
    - Pro tier: 24 hours
    - Starter tier: 48 hours
  - Handled by founder

**Part-Time Customer Success (Month 7+)**
- 15 hours/week @ €25/hour
- **Cost: €1,500/month**
- **Covers:** Help center article creation, onboarding documentation, 
  phone support for Pro tier, email triage

---

## Technology Alternatives Considered

### CMS Alternatives

**Directus**
- **Pros:** Better UI, real-time WebSocket support, mature community
- **Cons:** Requires separate deployment (Vue.js, not Next.js native), no official multi-tenant plugin
- **Decision:** Payload chosen for Next.js integration and built-in multi-tenancy

**Strapi**
- **Pros:** Most popular headless CMS, large community, good documentation
- **Cons:** Lacks native multi-tenancy, would require $15k+ custom development, slower performance
- **Decision:** Multi-tenancy requirement eliminated Strapi

**KeystoneJS**
- **Pros:** GraphQL-first, good TypeScript support
- **Cons:** Smaller community, fewer plugins, less mature than competitors
- **Decision:** Less proven at scale

---

### Database Alternatives

**MongoDB**
- **Pros:** Flexible schema, fast writes, Payload originally built for MongoDB
- **Cons:** Volleyball data is relational (teams/players/schedules), joins are expensive
- **Decision:** PostgreSQL better suited for relational sports data

**MySQL**
- **Pros:** Mature, widely supported
- **Cons:** Less advanced features than PostgreSQL (no JSONB, weaker full-text search)
- **Decision:** PostgreSQL chosen for superior features

**Supabase (PostgreSQL)**
- **Pros:** PostgreSQL with real-time features, generous free tier
- **Cons:** More expensive than Neon at scale, less established
- **Decision:** Neon chosen for serverless PostgreSQL and better pricing

---

### Deployment Alternatives

**Netlify**
- **Pros:** Similar to Vercel, good for static sites
- **Cons:** Less optimized for Next.js (built by competitor), serverless functions more limited
- **Decision:** Vercel is first-class Next.js support

**Railway**
- **Pros:** Better control, includes database, competitive pricing
- **Cons:** Requires more DevOps knowledge, no free tier
- **Decision:** Start with Vercel for simplicity, migrate to Railway if needed at 100+ clubs

**AWS (ECS Fargate + RDS)**
- **Pros:** Maximum control, best performance at scale, trusted infrastructure
- **Cons:** Expensive, complex, requires significant DevOps expertise
- **Decision:** Too complex for initial launch, consider at 200+ clubs

---

## Version Lock File

**Critical Versions (as of January 2025):**

```json
{
  "payload": "^3.0.0",
  "next": "^15.0.0",
  "react": "^19.0.0",
  "typescript": "^5.3.0",
  "drizzle-orm": "^0.33.0",
  "@payloadcms/db-postgres": "^3.0.0",
  "@payloadcms/plugin-multi-tenant": "^3.0.0",
  "@payloadcms/plugin-stripe": "^3.0.0",
  "@payloadcms/richtext-lexical": "^3.0.0",
  "tailwindcss": "^3.4.0",
  "@radix-ui/react-*": "^1.0.0",
  "zod": "^3.22.0",
  "react-hook-form": "^7.48.0"
}
```

---

## Conclusion

This technical stack provides a modern, scalable foundation for a multi-tenant volleyball club SaaS platform. Key strengths:

- **Unified Architecture:** Payload + Next.js eliminates frontend/backend separation
- **Cost-Effective:** $146/month serves 50 clubs (96.3% gross margin at $79/month pricing)
- **Developer Experience:** TypeScript throughout, excellent tooling, fast iteration
- **Scalable:** Architecture supports 5 to 500+ clubs without fundamental changes
- **Production-Ready:** Battle-tested technologies used by Disney, Microsoft, Vercel

**Migration Path:**
- **0-100 clubs:** Vercel + Neon (serverless)
- **100-300 clubs:** Railway (containers)
- **300+ clubs:** AWS ECS + RDS (full control)

The stack is flexible, well-documented, and positioned for long-term success.