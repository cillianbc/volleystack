# System Patterns & Architecture

**Project:** VolleyStack - Volleyball Club SaaS Platform  
**Document Version:** 1.0  
**Last Updated:** January 2025

---

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Multi-Tenancy Patterns](#multi-tenancy-patterns)
3. [Data Access Patterns](#data-access-patterns)
4. [Authentication & Authorization Patterns](#authentication--authorization-patterns)
5. [API Integration Patterns](#api-integration-patterns)
6. [Caching Patterns](#caching-patterns)
7. [Background Job Patterns](#background-job-patterns)
8. [File Upload & Storage Patterns](#file-upload--storage-patterns)
9. [Email & Notification Patterns](#email--notification-patterns)
10. [Error Handling & Resilience Patterns](#error-handling--resilience-patterns)
11. [Testing Patterns](#testing-patterns)
12. [Monitoring & Observability Patterns](#monitoring--observability-patterns)

---

## Architecture Overview

### High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ Club Website │  │ Admin Panel  │  │ Mobile       │         │
│  │ (Public)     │  │ (Protected)  │  │ (Future)     │         │
│  └───────┬──────┘  └───────┬──────┘  └──────┬───────┘         │
└──────────┼─────────────────┼────────────────┼──────────────────┘
           │                 │                │
           │     HTTPS/TLS 1.3 over CDN       │
           │                 │                │
┌──────────▼─────────────────▼────────────────▼──────────────────┐
│                  VERCEL EDGE NETWORK                            │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Edge Functions (Middleware)                             │  │
│  │  - Tenant Resolution (subdomain/domain → tenant_id)      │  │
│  │  - Rate Limiting (per tenant)                            │  │
│  │  - Security Headers                                      │  │
│  │  - A/B Testing (future)                                  │  │
│  └──────────────────────────────────────────────────────────┘  │
└──────────┬──────────────────────────────────────────────────────┘
           │
┌──────────▼──────────────────────────────────────────────────────┐
│              APPLICATION LAYER (Next.js 15 + Payload 3.0)       │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Next.js App Router                                        │ │
│  │  ┌─────────────────┐  ┌──────────────────┐                │ │
│  │  │ Public Routes   │  │ API Routes       │                │ │
│  │  │ - Club Pages    │  │ - Webhooks       │                │ │
│  │  │ - Team Rosters  │  │ - Custom Logic   │                │ │
│  │  │ - Schedules     │  │ - Health Checks  │                │ │
│  │  └─────────────────┘  └──────────────────┘                │ │
│  │                                                             │ │
│  │  ┌─────────────────┐  ┌──────────────────┐                │ │
│  │  │ Payload Admin   │  │ Payload REST API │                │ │
│  │  │ - Content Mgmt  │  │ - Collections    │                │ │
│  │  │ - Dashboard     │  │ - Auth Endpoints │                │ │
│  │  └─────────────────┘  └──────────────────┘                │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Payload Core Services                                     │ │
│  │  - Multi-Tenant Access Control                             │ │
│  │  - Local API (Server-side DB access)                       │ │
│  │  - Jobs Queue (Background Tasks)                           │ │
│  │  - File Upload Handler                                     │ │
│  │  - Webhook Manager                                         │ │
│  └────────────────────────────────────────────────────────────┘ │
└──────────┬──────────────┬────────────────┬─────────────────────┘
           │              │                │
    ┌──────▼─────┐ ┌─────▼──────┐ ┌──────▼──────┐
    │ PostgreSQL │ │ Blob       │ │ Email       │
    │ (Neon)     │ │ Storage    │ │ (Resend)    │
    │            │ │ (Vercel)   │ │             │
    └────────────┘ └────────────┘ └─────────────┘
           │
    ┌──────▼─────────────────────────┐
    │  Database Schema               │
    │  ┌───────────┐  ┌────────────┐ │
    │  │  public   │  │ tenant_xyz │ │
    │  │ (platform)│  │  (club)    │ │
    │  └───────────┘  └────────────┘ │
    └────────────────────────────────┘
           
┌────────────────────────────────────────────────────────────────┐
│                    EXTERNAL SERVICES                           │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐      │
│  │ Stripe   │  │ n8n      │  │ League   │  │ Social   │      │
│  │ (Payment)│  │(Workflow)│  │ API      │  │ Media    │      │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘      │
└────────────────────────────────────────────────────────────────┘
```

---

## Multi-Tenancy Patterns

### Pattern 1: Tenant Identification & Resolution

**Problem:** How do we identify which club (tenant) a request belongs to?

**Solution:** Multi-level tenant resolution with fallback hierarchy


### Pattern 2: Database Schema Isolation

**Problem:** How do we ensure data isolation between tenants in PostgreSQL?

**Solution:** Separate PostgreSQL schemas per tenant with automatic search path

**Schema Structure:**

**Automatic Search Path Setting:**

**Payload Hook Application:**


### Pattern 3: Multi-Tenant Access Control

**Problem:** How do we ensure users can only access data from their authorized tenants?

**Solution:** Payload's access control combined with tenant context

**Access Control Implementation:**


### Pattern 4: Manual Tenant Provisioning (Pilot Phase)

**Problem:** How do we onboard clubs during pilot without complex automation?

**Solution:** Manual provisioning workflow with founder involvement

**Provisioning Flow:**

1. **Club Inquiry:** Club expresses interest (email, phone, referral)

2. **Founder Qualification:**
   - Verify club is Volleyball Ireland member
   - Assess fit (team count, technical capability, budget)
   - Demo platform via screen share
   - Answer questions about league integration

3. **Manual Setup:**
   - Generate Stripe customer and subscription (12-month contract)
   - Send invoice manually via Stripe dashboard (14-day payment terms)
   - Await payment confirmation

4. **Provisioning (after payment received):**
   - Manually create tenant record in admin panel
   - Generate PostgreSQL schema
   - Create admin user with temporary password
   - Seed default pages (Home, Teams, About, Contact, History)
   - Send welcome email with login credentials

5. **Onboarding Call (1 hour):**
   - Walk through admin panel
   - Upload logo and set brand colors
   - Assist with team-to-league matching:
     - Show league teams containing club name
     - Link internal teams to external league teams
     - Explain sync behavior
     - Clarify that custom teams won't auto-sync
   - Guide through CSV roster import:
     - Download CSV template (pre-filled with team names)
     - Explain required vs optional fields
     - Import CSV together
     - Upload sample player photos
   - Create first news post together
   - Review site preview
   - Set expectations for support response times

6. **Follow-up:**
   - Day 3: Check-in email (any questions?)
   - Day 7: Phone call (how's it going?)
   - Day 14: Feedback survey
   - Week 4: Review usage, offer additional training

**Future Migration Path:**
- After 10 pilot clubs, identify most common onboarding friction points
- Automate repetitive steps (schema creation, default content seeding)
- Build self-service signup flow (Weeks 16-20)
- Migrate to automated provisioning at 25+ clubs

## Data Access Patterns

### Pattern 1: Repository Pattern with Local API

**Problem:** How do we efficiently query data without HTTP overhead?

**Solution:** Payload's Local API for server-side data access



**Usage in Server Components:**


### Pattern 2: Optimistic Updates with Server Actions

**Problem:** How do we provide instant feedback while ensuring data consistency?

**Solution:** Server Actions with optimistic UI updates


## Authentication & Authorization Patterns

### Pattern 1: JWT with Secure Cookies

**Problem:** How do we securely authenticate users across multi-tenant system?

**Solution:** Payload's built-in JWT auth with HTTP-only cookies

**Login Flow:**

// Payload handles this internally, but conceptually:
// 1. User submits email + password
// 2. Payload verifies credentials against users table
// 3. Generates JWT with user ID, roles, tenant associations
// 4. Sets HTTP-only cookie with JWT
// 5. Returns user object (without password)


### Pattern 2: Tenant Switching for Super Admins

**Problem:** How do super admins switch between tenants without re-authenticating?

**Solution:** Cookie-based active tenant with validation


**Admin Panel Tenant Selector:**

---

## API Integration Patterns

### Pattern 1: Team-to-League Matching

**Problem:** How do clubs link their internal teams to league teams for auto-sync?

**Solution:** Name-based matching with manual confirmation

**Matching Algorithm:**

1. **Discover league teams:**
   - Query all divisions in league standings/fixtures APIs
   - Filter teams where name contains club identifier
   - Example: Club slug = "eagles" → Find all teams with "Eagles" in name
     - "Eagles VBC - Women's Premier"
     - "Eagles VBC - Men's Division 1"
     - "Eagles VBC - Women's Division 2"

2. **Present to admin during onboarding:**
   - Show list of discovered league teams
   - For each, admin can:
     - Create internal team linked to this league team
     - Skip (team not active this season)
     - Manually search if wrong match

3. **Create team-league association:**
   - Store mapping in `team_league_associations` table
   - Fields:
     - `internal_team_id` → Internal team record
     - `external_team_id` → League API team identifier
     - `sync_schedules` → Boolean (default: true)
     - `sync_standings` → Boolean (default: true)
     - `last_synced_at` → Timestamp
     - `sync_enabled` → Boolean (default: true)

**Custom Teams (Not in League):**
- Admin can create teams without league linkage
- Use case: Development squads, social teams, indoor vs beach teams
- Admin panel shows warning: "⚠️ Auto-sync disabled - not linked to league"
- Can manually add schedules and results for these teams

**Unlinking Teams:**
- Admin can unlink team from league at any time
- Historical data preserved (schedules, standings snapshots remain)
- Future syncing stops
- Can re-link to same or different league team later

**Re-linking Teams:**
- If league team name changes mid-season, admin can update link
- System prompts: "Continue syncing with new team name?"
- Preserves all historical associations

### Pattern 2: External API Polling with Background Jobs

**Problem:** How do we sync data from league APIs without blocking user requests?

**Solution:** Scheduled background jobs with error handling and caching


### Pattern 3: Rate Limiting for External APIs

**Problem:** How do we avoid hitting rate limits on external APIs?

**Solution:** Token bucket algorithm with Redis-backed counters


### Pattern 4: Circuit Breaker for Resilient Integrations

**Problem:** How do we handle external API failures gracefully without cascading failures?

**Solution:** Circuit breaker pattern with fallback to cached data


## Caching Patterns


### Pattern 1: Multi-Layer Cache Strategy


## Background Job Patterns
### Pattern 1: Nightly League Data Sync

**Problem:** How often should we sync league data?

**Solution:** Nightly sync at 2:00 AM UTC

**Rationale:**
- League standings update: Sunday nights after weekend matches
- League fixtures update: Monday nights (rare mid-season changes)
- Syncing every 15 minutes would be wasteful (API doesn't change)
- Nightly sync ensures fresh data without excessive API calls
- Off-peak timing reduces load on both systems

**Sync Job Flow:**

2:00 AM UTC - League Sync Job Triggered

For each tenant with active subscription:
  For each team with sync_enabled = true:
    
    1. Fetch fixtures from league API (by external_team_id)
    2. Upsert schedules:
       - Match on external_api_id
       - Update if exists, insert if new
       - Mark as synced
    
    3. Fetch standings from league API
    4. Create new standings snapshot
    5. Update team_league_association.last_synced_at
    
    6. If error occurs:
       - Log error with tenant_id and team_id
       - Continue to next team (don't fail entire job)
       - Alert admin if same team fails 3 nights in a row

7:00 AM UTC - Sync completion report emailed to admin
- Total teams synced: 25
- Successful: 24
- Failed: 1 (Eagles VBC - Women's Div 2, reason: API timeout)

### Pattern 2: Job Queue with Retry Logic



## File Upload & Storage Patterns

### Pattern 1: Direct Upload to Blob Storage


## Testing Patterns

### Pattern 1: Multi-Tenant Test Isolation

**Problem:** How do we test multi-tenant features without data contamination?

**Solution:** Dedicated test schemas with automatic cleanup


### Pattern 2: E2E Testing with Playwright

**Problem:** How do we test complete user workflows across multi-tenant scenarios?

**Solution:** Page Object Model with tenant context



### Pattern 3: API Contract Testing

**Problem:** How do we ensure API consistency across tenant boundaries?

**Solution:** OpenAPI schema validation with automated tests


## Monitoring & Observability Patterns

### Pattern 1: Structured Logging with Context

**Problem:** How do we debug issues across multi-tenant infrastructure?

**Solution:** Structured logs with tenant and user context

**Log Query Examples (via Vercel Logs or Datadog):**
```
// Find all errors for specific tenant
tenantId:"tenant-123" AND level:"error"

// Find slow database queries
duration:>1000 AND action:"database_query"

// Trace request across services
requestId:"req-abc-123"
```

---

### Pattern 2: Custom Metrics and Dashboards

**Problem:** How do we track business and technical metrics per tenant?

**Solution:** Custom metrics with tenant tags sent to monitoring service



**Custom Dashboard Queries:**
```
# Average page load time per tenant
avg(page.load_time) by tenantId

# Active clubs (posted content in last 7 days)
count(post.created) where timestamp > now-7d group by tenantId

# Churn indicators (no admin logins in 14 days)
count(admin.login) where timestamp > now-14d group by tenantId having count < 1
```

---

### Pattern 3: Health Checks and Uptime Monitoring

**Problem:** How do we detect and respond to outages quickly?

**Solution:** Comprehensive health check endpoint with dependency status


## Performance Optimization Patterns

### Pattern 1: Database Query Optimization

**Problem:** How do we keep queries fast as tenant data grows?

**Solution:** Strategic indexing and query optimization



**Query Monitoring:**



### Pattern 2: Image Optimization Pipeline

**Problem:** How do we serve images efficiently to users globally?

**Solution:** Automatic image optimization with multiple formats and sizes



**Cloudinary Integration (Future Enhancement):**


### Pattern 3: API Response Caching

**Problem:** How do we reduce database load for frequently accessed data?

**Solution:** HTTP caching with revalidation tags



## Migration & Rollback Patterns

### Pattern 1: Zero-Downtime Database Migrations

**Problem:** How do we update database schema without downtime?

**Solution:** Expand-Contract migration pattern



### Pattern 2: Feature Flags for Safe Rollouts

**Problem:** How do we deploy new features safely with ability to instantly rollback?

**Solution:** Feature flags with percentage-based rollout


**Rollout Strategy:**
1. Week 1: Enable for 5% of tenants, monitor metrics
2. Week 2: Increase to 25% if no issues
3. Week 3: Increase to 50%
4. Week 4: Enable for 100% if metrics are positive

**Instant Rollback:**


## Backup & Recovery Patterns

### Pattern 1: Automated Database Backups

**Problem:** How do we ensure data can be recovered if corrupted or lost?

**Solution:** Multi-tier backup strategy with regular testing

**Implementation:**

**Neon Automatic Backups:**
- Point-in-time recovery (PITR) up to 7 days (Scale plan)
- Daily snapshots retained for 7 days
- No configuration needed

**Additional Backup Strategy:**


**Backup Retention Policy:**
- Daily backups: Retained for 30 days
- Weekly backups (Sunday): Retained for 90 days
- Monthly backups (1st): Retained for 1 year

---

### Pattern 2: Disaster Recovery Runbook

**Problem:** How do we recover quickly from major outages?

**Solution:** Documented procedures with regular drills

**DR Runbook:**

```markdown
# Disaster Recovery Procedures

## Scenario 1: Database Corruption

### Symptoms
- Database queries returning errors
- Data inconsistencies reported by users
- Unable to connect to database

### Recovery Steps

1. **Assess Impact** (5 minutes)
   - Check Neon dashboard for database status
   - Query health check endpoint: `/api/health`
   - Identify affected tenants

2. **Enable Maintenance Mode** (2 minutes)
   ```bash
   vercel env add MAINTENANCE_MODE true
   vercel deploy --prod
   ```

3. **Restore from Backup** (15-30 minutes)
   ```bash
   # Option A: Neon Point-in-Time Recovery
   neon restore --timestamp "2025-01-15 14:30:00"
   
   # Option B: S3 Backup Restore
   aws s3 cp s3://volleystack-backups/database/latest.sql.gz ./
   gunzip latest.sql.gz
   psql $DATABASE_URI < latest.sql
   ```

4. **Verify Data Integrity** (10 minutes)
   - Run integrity checks
   - Test key workflows (login, view schedules, create team)
   - Check 5 random tenants for data consistency

5. **Disable Maintenance Mode** (2 minutes)
   ```bash
   vercel env remove MAINTENANCE_MODE
   vercel deploy --prod
   ```

6. **Post-Mortem** (within 24 hours)
   - Document root cause
   - Update monitoring to prevent recurrence
   - Communicate to affected customers

## Scenario 2: Complete Vercel Outage

### Symptoms
- All sites returning 503 errors
- Vercel dashboard showing outage

### Recovery Steps

1. **Verify Outage** (2 minutes)
   - Check Vercel status page
   - Test health endpoint from external monitor

2. **Activate Backup Hosting** (Railway - 30 minutes)
   ```bash
   # Deploy to Railway
   git push railway main
   
   # Update DNS to point to Railway
   # Update A record: @ -> railway IP
   # TTL: 300 seconds (5 minutes)
   ```

3. **Monitor DNS Propagation** (5-30 minutes)
   ```bash
   dig volleystack.com
   # Verify points to Railway IP
   ```

4. **Notify Customers** (immediate)
   - Status page update
   - Email to all club admins
   - Social media update

5. **When Vercel Recovers**
   - Sync any data created on Railway back to Vercel
   - Switch DNS back to Vercel
   - Monitor for 24 hours

## Testing Schedule

- **Monthly**: Test database restore
- **Quarterly**: Full DR drill (simulate major outage)
- **Annually**: Review and update DR procedures

## Security Patterns (Expanded)

### Pattern 1: Input Sanitization

**Problem:** How do we prevent XSS and injection attacks?

**Solution:** Multi-layer input validation and sanitization

**Implementation:**



### Pattern 2: Rate Limiting per Tenant

**Problem:** How do we prevent abuse without affecting legitimate users?

**Solution:** Token bucket rate limiting with tenant isolation


## Conclusion

These system patterns provide a comprehensive foundation for building, operating, and scaling the VolleyStack platform. Key principles:

- **Multi-Tenancy First:** All patterns designed with tenant isolation as primary concern
- **Resilience:** Circuit breakers, retries, and graceful degradation prevent cascading failures
- **Observability:** Structured logging and metrics enable quick debugging and optimization
- **Security:** Defense in depth with validation, sanitization, and rate limiting
- **Performance:** Caching strategies and query optimization maintain speed at scale
- **Reliability:** Automated backups, tested disaster recovery, and zero-downtime deployments

**Implementation Priority:**
1. **Phase 1 (Essential):** Multi-tenancy, authentication, basic monitoring
2. **Phase 2 (Growth):** Caching, background jobs, external integrations
3. **Phase 3 (Scale):** Advanced monitoring, performance optimization, disaster recovery
4. **Phase 4 (Enterprise):** Feature flags, A/B testing, multi-region deployment

These patterns are battle-tested in production SaaS applications and will support VolleyStack from 5 to 500+ clubs.