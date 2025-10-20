# Project Brief: Volleyball Club SaaS Platform

**Project Name:** VolleyStack (working title)  
**Project Type:** Multi-Tenant SaaS Web Platform  
**Target Launch:** Q2 2025  
**Document Version:** 1.0  
**Last Updated:** January 2025

---

## Executive Summary

VolleyStack is a turnkey website solution for volleyball clubs, providing a template-based SaaS platform that enables clubs to establish professional web presence without technical expertise or development costs. The platform offers standardized features essential to volleyball clubs—team rosters, game schedules, news updates, and member engagement—while allowing customization of branding and content.

**Business Model:** Subscription-based SaaS charging clubs $25-50/month for hosting, platform access, and ongoing support.

**Market Opportunity:** 50 clubs in initial target market, expanding to 200+ clubs within 18 months.

**Technical Approach:** Headless CMS architecture (Payload + Next.js + PostgreSQL) providing modern multi-tenant infrastructure with automated provisioning, external API integrations, and serverless deployment.

**Investment Required:** $12,000-18,000 initial development (3-4 months), $143/month operational infrastructure at 50 clubs.

**Projected Revenue:** $1,750 /year at 50 clubs ($35/month tier), scaling to $7,000/year at 200 clubs.

---

## Problem Statement

### Current Pain Points for Volleyball Clubs

**Lack of Technical Resources**
- Clubs are staffed by volunteers with limited technical expertise
- No budget for professional web developers or agencies
- Cannot maintain complex websites or troubleshoot technical issues

**Expensive or Inadequate Solutions**
- Custom development costs $5,000-15,000 upfront
- Generic website builders (Wix, Squarespace) lack sports-specific features
- Existing sports platforms are too complex or expensive for local clubs

**Manual Data Management**
- Manually updating schedules, rosters, and results is time-consuming
- Information scattered across Facebook, emails, printed flyers
- Parents/members frustrated by difficulty finding current information

**Limited Member Engagement**
- No centralized hub for club communications
- Missed opportunities to showcase achievements and build community
- Difficulty recruiting new members without professional web presence

### Market Validation

- **Target users confirmed pain points:** Interviews with 15 volleyball clubs revealed 12/15 use Facebook as primary communication channel, citing website costs/complexity as barrier
- **Willingness to pay:** 10/15 indicated $75-100/month acceptable for "all-in-one solution"
- **Feature priorities:** Schedule management, team rosters, and news/announcements identified as "must-haves"
- **Competitive gap:** No volleyball-specific template solutions exist at accessible price points

---

## Solution Overview

### Product Description

VolleyStack provides each club with a fully-functional, professionally designed website including:

**Core Features (All Plans)**
- Team roster management with player profiles and photos
- Game schedule display with automated league data synchronization
- News/announcement blog with rich media support
- Mobile-responsive design optimized for parents viewing on phones
- Custom branding (club colors, logo)
- Payment portal integration via Stripe for club registrations/fees

**Administrative Capabilities**
- Intuitive admin panel for content management (no coding required)
- CSV roster import with photo upload
- Automated schedule updates from league APIs (nightly sync)
- Rich text page editor
- Media library for photos/videos
- User role management (admin, coach, viewer)

**Default Pages (Auto-created)**
- Homepage (published)
- Teams page (published)
- About page (draft template)
- Contact page (draft template)
- History page (draft template)

**Platform Infrastructure**
- 99.9% uptime guarantee
- Automatic backups and security updates
- SSL certificates and HTTPS encryption
- Global CDN for fast page loads
- Email support with 24-hour response time

### Key Differentiators

1. **Volleyball-Specific Design:** Purpose-built for volleyball clubs, not generic sports or general websites
2. **External API Integrations:** Automated data sync from league APIs eliminates manual schedule entry
3. **True Multi-Tenancy:** Unified platform infrastructure reduces costs, passes savings to clubs
4. **Developer-Grade Technology:** Modern tech stack provides performance and reliability typically reserved for enterprise clients
5. **Zero Technical Debt:** Clubs inherit platform improvements automatically—no legacy website abandonment

---

## Business Model

### Revenue Streams

**Primary: Monthly Subscriptions**
- **Starter Plan:** €35/month
  - 1 website with subdomain (clubname.volleystack.com)
  - Up to 5 teams
  - 10GB storage
  - Email & phone support (48-hour response SLA)
  
- **Pro Plan:** €79/month
  - All Starter features
  - Custom domain (clubname.com)
  - Unlimited teams
  - 50GB storage
  - Priority email & phone support (24-hour response SLA)
  - Advanced analytics dashboard  

**Secondary: Setup Fees**
- One-time setup fee: $TBC
- Covers initial website configuration, logo/branding setup, content migration
- Provides 8-10 hours of dedicated onboarding assistance
- Optional: Additional custom design work at $75/hour


### Pricing Strategy Rationale

**Market Positioning:** Premium value at accessible price point
- Undercuts custom development by 90% ($35/month vs. $5,000+ upfront)
- Comparable to generic website builders but with volleyball-specific features
- Higher than basic hosting but includes platform, support, and automation

**Cost Structure Validation**
- Infrastructure cost per club: $2.87/month at scale
- Customer acquisition cost: $150 estimated (content marketing, referrals)
- Payback period: 2 months
- Customer lifetime value: $1,050 (assuming 30-month average tenure)

**Volume Pricing Path**
- Years 1-2: $35/month to build market share and gather testimonials
- Year 3+: Expand to other countries

---

## Target Market

### Primary Market: Local Volleyball Clubs

**Geographic Focus (Phase 1):**
- Ireland: 50 clubs identified as initial target out of 150
- UK expansion: 150+ clubs (Phase 2, Month 12+)
- Regional associations and club networks

**Club Characteristics:**
- 100-500 members
- 3-15 teams across age groups (U12, U14, U16, U18, adult)
- Volunteer-run with 1-2 paid administrators
- Annual budget: €20,000-100,000
- Currently using Facebook/email,whatsapp as primary communication

**Decision Makers:**
- Club chairperson/president
- Secretary/administrator
- Communications officer
- Budget typically requires committee approval

### Secondary Market: Adjacent Sports

**Expansion Opportunities (Year 2+):**
- Basketball clubs
- Netball clubs
- Handball clubs
- Badminton clubs
- Multi-sport community centers

**Template Adaptability:**
- Core features (rosters, schedules, news) apply universally to team sports
- Minor customization needed for sport-specific terminology
- Expands addressable market 10x while leveraging same infrastructure

---

## Success Metrics

### Key Performance Indicators (KPIs)

**Business Metrics**
- **Monthly Recurring Revenue (MRR):** Target $1,450 at 50 clubs by Month 6
- **Customer Acquisition Cost (CAC):** Target <$150 per club
- **Churn Rate:** Target <10% annually (>90% retention)
- **Net Revenue Retention:** Target >100% (upsells offset churn)
- **Gross Margin:** Target >90% (achieved: 96.4% at current pricing)

**Product Metrics**
- **Time to First Value:** <2 hours from signup to published website
- **Admin Panel Usage:** >80% of clubs log in weekly
- **Feature Adoption:** 
  - Schedule sync: >90% of clubs
  - Social feed integration: >60% of clubs
  - Email newsletters: >40% of clubs
- **Website Performance:** 
  - Page load time: <2 seconds (p95)
  - Uptime: >99.9%
  - Lighthouse score: >90

**Customer Success Metrics**
- **Customer Satisfaction (CSAT):** Target >4.5/5.0
- **Net Promoter Score (NPS):** Target >50
- **Support Ticket Volume:** <2 tickets per club per month
- **Referral Rate:** >20% of new customers from referrals

### Success Criteria by Phase

**Phase 1 (Months 1-4): Build & Validate**
- ✅ Launch MVP with 5 core features
- ✅ Onboard 5 pilot clubs
- ✅ Achieve <3 second page load times
- ✅ Collect qualitative feedback from pilot users
- ✅ Refine onboarding process to <4 hours setup time

**Phase 2 (Months 5-8): Scale to 25 Clubs**
- ✅ Onboard 20 additional clubs
- ✅ Achieve $1,975 MRR ($79 × 25 clubs)
- ✅ Establish content marketing presence (blog, social media)
- ✅ Implement automated league data sync for 2+ governing bodies
- ✅ Maintain <5% churn in pilot cohort

**Phase 3 (Months 9-12): Scale to 50 Clubs**
- ✅ Onboard 25 additional clubs (50 total)
- ✅ Achieve $3,950 MRR
- ✅ Launch referral program generating 10+ leads
- ✅ Expand support capacity (hire part-time CS rep)
- ✅ Release 3 new features based on customer feedback

**Phase 4 (Months 13-18): Expand Market**
- ✅ Enter UK market, onboard 30 clubs (80 total)
- ✅ Achieve $6,320 MRR
- ✅ Launch Pro plan tier, convert 10% of customers
- ✅ Build integration partnerships with 2+ league management platforms
- ✅ Achieve profitability after salaries

---

## Project Stakeholders

### Internal Team

**Technical Lead (You)**
- Product vision and strategy
- Technical architecture and development
- DevOps and infrastructure management
- Platform maintenance and feature development
- Hours: Full-time (Months 1-4), 15-20 hours/week (Month 5+)

**Customer Success / Support (TBD - Month 6)**
- Client onboarding and training
- Tier 1 support ticket management
- Customer success check-ins
- Documentation and knowledge base maintenance
- Hours: Part-time 10-15 hours/week initially, scaling to full-time at 75+ clubs

**Sales / Marketing (TBD - Month 9)**
- Inbound lead qualification
- Sales calls and demos
- Content marketing (blog posts, case studies)
- Social media management
- Partnership development
- Hours: Contract or part-time 10-20 hours/week

### External Partners

**Governing Body APIs**
- Volleyball Ireland (or regional equivalent)
- League management platforms
- Schedule/standings data providers

**Infrastructure Vendors**
- Vercel (hosting platform)
- Neon (PostgreSQL database)
- Stripe (payment processing)
- Resend (transactional email)

### Customers (Volleyball Clubs)

**Primary User:** Club Administrator
- Manages content updates (news, rosters, schedules)
- Responds to parent/member inquiries
- Reviews website analytics
- Manages user accounts for coaches

**Secondary Users:** Coaches
- Updates team rosters and player information
- Views schedules and results
- Limited content publishing access

**End Users:** Parents, Players, Members
- View schedules and rosters
- Read news and announcements
- Access contact information
- Register for programs (future)

---

## Project Timeline

### Phase 1: Foundation (Weeks 1-4)


**Week 1: Project Setup**
- Initialize Payload + Next.js codebase
- Configure development environment (local PostgreSQL)
- Set up version control (GitHub) and project management
- Deploy staging environment (Vercel + Neon free tiers)

**Week 2: Core Data Models**
- Implement multi-tenant plugin configuration
- Build Tenants, Users, Teams, Players, Schedules collections
- Configure access control and data isolation
- Initial admin panel customization

**Week 3: Frontend Template**
- Design system implementation (Tailwind + shadcn/ui)
- Single responsive template with customizable colors/logo
- Default pages: Homepage, Teams, About, Contact, History
- Rich text editor for page content
- Navigation menu management

**Week 4: Admin Experience**
- Custom dashboard with key metrics
- CSV import functionality for rosters
- Team-to-league matching interface
- Media library organization
- Testing with sample data for 3 mock clubs

**Deliverable:** Working MVP with core features, deployable to staging

---

### Phase 2: Integrations (Weeks 5-8)

**Week 5: Stripe Integration**
- Configure Stripe for manual invoicing
- Monthly payment collection (12-month commitment)
- Webhook handlers (payment success, payment failure)
- Grace period logic for failed payments (21-day cycle)
- Admin billing dashboard

**Week 6: League API Integration**
- Team matching interface (link internal teams to league teams)
- Nightly background sync job for fixtures and standings
- Historical data preservation on team unlinking
- Error handling and retry logic
- Admin UI for manual refresh trigger

**Week 7: Public Website Polish**
- Finalize default page templates
- Team roster display with league standings integration
- Schedule page with automatic updates
- News/blog layout
- Contact form (via Resend)

**Week 8: Email & Documentation**
- Configure Resend for transactional emails
- Welcome email (manual provisioning notification)
- Password reset flow
- Payment reminder emails
- Create help center documentation (searchable articles)
- DNS setup guide for custom domains
- CSV import template and instructions

**Deliverable:** Fully integrated platform ready for manual pilot onboarding

---

### Phase 3: Pilot Launch (Weeks 9-12)

**Week 9: Performance & Security**
- Image optimization pipeline
- Database query optimization and indexing
- Security audit (OWASP Top 10, access control validation)
- Load testing (simulate 10 concurrent clubs)
- Backup and disaster recovery testing

**Week 10: Testing & Polish**
- E2E testing for critical paths (create team, import roster, publish page)
- Multi-tenant isolation testing
- Payment flow testing (invoice → payment → activation)
- User acceptance testing with 1-2 friendly clubs

**Week 11: Documentation & Setup**
- Complete admin user guide with screenshots
- CSV import guide with templates
- Custom domain setup guide (A record + CNAME + TXT verification)
- FAQ documentation
- Create starter content templates for default pages

**Week 12: Pilot Launch (3 Clubs)**
- Manual provisioning for first 3 clubs
- Personal onboarding calls (1 hour per club)
- Assist with:
  - Team-to-league matching
  - CSV roster import
  - Logo upload and color customization
  - Initial content creation
- Daily check-ins during first week
- Gather structured feedback

**Deliverable:** 3 active pilot clubs with validated workflows

**Month 4-5: Expand Pilot**
- Add 2 more clubs based on initial learnings
- Refine onboarding process
- Fix critical bugs and UX issues
- Begin documenting automation requirements for self-service onboarding

### Phase 4: Growth (Months 4-6)

**Month 4: Scale to 20 Clubs**
- Onboard 15 additional clubs (batches of 5/week)
- Implement feedback from pilot phase
- Begin content marketing (blog posts, case studies)
- Establish support processes and documentation
- Monitor infrastructure performance under increased load

**Month 5: Scale to 40 Clubs**
- Onboard 20 additional clubs (batches of 10/week)
- Launch referral program (give $50 credit, get $50 credit)
- Hire part-time customer success representative
- Build relationships with regional volleyball associations
- Optimize onboarding automation to reduce setup time

**Month 6: Reach 50 Clubs Milestone**
- Onboard final 10 clubs to hit initial target
- Comprehensive analytics review (churn, NPS, feature usage)
- Plan roadmap for next 6 months based on data
- Consider infrastructure migration (stay on Vercel vs. Railway)
- Celebrate milestone, gather testimonials and case studies

**Deliverable:** 50 active paying clubs, profitable operations, validated product-market fit

---

### Phase 5: Expansion (Months 7-12)

**Goals:**
- Expand to UK market (30+ clubs)
- Launch Pro tier pricing ($149/month)
- Build 2-3 partnership integrations (league management systems)
- Release mobile app (future consideration)
- Explore adjacent sports markets (basketball, netball)

---

## Budget & Resources

### Development Phase Investment (Months 1-4)

**Labor (Self-Funded)**
- 400-500 hours development @ $75/hour (opportunity cost)
- **Value: $30,000-37,500** if outsourced
- **Actual Cost: $0** (sweat equity)

**Infrastructure (Minimal)**
- Domain registration: $12/year
- Development tools (free tiers): $0/month
- **Total: $12**

**Marketing & Business Setup**
- Logo design (Fiverr/99designs): $50-200
- Legal (terms of service, privacy policy templates): $100-300
- **Total: $150-500**

**Total Phase 1 Investment: $162-512 out-of-pocket**

---

### Operational Costs (Recurring Monthly)

**At 5 Pilot Clubs (Months 4-5)**
- Vercel Hobby (free tier): $0
- Neon Free Tier: $0
- Resend (under 3k emails/month): $0
- n8n Cloud: $20
- Sentry (free tier): $0
- **Total: $20/month**

**At 25 Clubs (Months 6-8)**
- Vercel Pro: $20
- Neon Scale: $20
- Resend: $20
- n8n Cloud: $20
- Sentry: $26
- Vercel Blob Storage: $5
- **Total: $111/month**

**At 50 Clubs (Month 9+)**
- Vercel Pro: $20
- Neon Scale: $50
- Resend: $20
- n8n Cloud: $20
- Sentry: $26
- Vercel Blob Storage: $10
- **Total: $146/month**

**At 100 Clubs (Month 12+)**
- Railway or AWS Fargate: $200
- Neon Business or RDS: $100
- Cloudinary: $89
- Email/monitoring: $80
- **Total: $469/month**

---

### Personnel Costs (Future)

**Part-Time Customer Success (Month 6+)**
- 15 hours/week @ $25/hour
- **Cost: $1,500/month**
- **Covers:** Onboarding, support tickets, documentation

**Full-Time Customer Success (Month 12+ at 75+ clubs)**
- 40 hours/week @ $25/hour
- **Cost: $4,000/month**
- **Covers:** All customer-facing operations, proactive success management

**Part-Time Marketing/Sales (Month 9+)**
- 15 hours/week @ $30/hour
- **Cost: $1,800/month**
- **Covers:** Content creation, lead qualification, sales calls

---

### Revenue Projections

**Conservative Scenario**

| Month | Clubs | MRR @ $79 | Infrastructure | Gross Profit | Margin |
|-------|-------|-----------|----------------|--------------|--------|
| 4 (Pilot) | 5 | $395 | $20 | $375 | 94.9% |
| 6 | 25 | $1,975 | $111 | $1,864 | 94.4% |
| 9 | 50 | $3,950 | $146 | $3,804 | 96.3% |
| 12 | 80 | $6,320 | $469 | $5,851 | 92.6% |

**After Personnel (Month 12)**
- MRR: $6,320
- Infrastructure: $469
- Personnel: $5,800 (full-time CS + part-time marketing)
- **Net Profit: $51/month** (break-even milestone)
- **Note:** Profitability achieved; ready to reinvest in growth

**Optimistic Scenario (10% Pro plan adoption)**

| Month | Starter | Pro | Total MRR | Gross Profit |
|-------|---------|-----|-----------|--------------|
| 12 | 72 @ $79 | 8 @ $149 | $6,880 | $6,411 (after infra) |
| 18 | 135 @ $79 | 15 @ $149 | $12,900 | $12,131 (after infra) |
| 24 | 180 @ $79 | 20 @ $149 | $17,200 | $16,131 (after infra) |

---

## Risk Assessment

### Technical Risks

**Risk: Database Performance Degradation at Scale**
- **Impact:** High (affects all clubs)
- **Likelihood:** Medium
- **Mitigation:** 
  - Implement database query monitoring (pganalyze)
  - Optimize indexes proactively
  - Plan migration path to dedicated database instances at 200+ clubs
  - Budget for connection pooling (PgBouncer) at 100+ clubs

**Risk: Third-Party API Changes Breaking Integrations**
- **Impact:** Medium (affects schedule sync, social feeds)
- **Likelihood:** Medium
- **Mitigation:**
  - Version lock API dependencies
  - Implement graceful degradation (fallback to cached data)
  - Monitor API health with external checks
  - Build abstraction layer for easy provider switching

**Risk: Vendor Lock-in with Vercel**
- **Impact:** Medium (migration complexity)
- **Likelihood:** Low
- **Mitigation:**
  - Payload + Next.js are open source, fully portable
  - Document migration path to Railway/AWS
  - Test deployment to alternative platforms quarterly
  - Use standard PostgreSQL (not Vercel-specific features)

---

### Business Risks

**Risk: Slow Customer Acquisition**
- **Impact:** High (delays profitability)
- **Likelihood:** Medium
- **Mitigation:**
  - Offer discounted pilot pricing ($49/month for first 10 clubs)
  - Partner with volleyball associations for bulk deals
  - Build case studies and referral incentives early
  - Focus on clubs where you have warm introductions

**Risk: High Churn Rate**
- **Impact:** High (undermines SaaS model)
- **Likelihood:** Low-Medium
- **Mitigation:**
  - Proactive customer success (monthly check-ins)
  - Continuously add features based on feedback
  - Annual payment discount (10% off, locks in commitment)
  - Early warning system (track login frequency, trigger intervention)

**Risk: Competitive Entry**
- **Impact:** Medium (price pressure, feature competition)
- **Likelihood:** Medium (market validates opportunity)
- **Mitigation:**
  - Move fast to establish brand and customer base
  - Build network effects (community, content library)
  - Focus on exceptional customer service as differentiator
  - Continuous innovation (2-3 new features per quarter)

**Risk: Payment Processing Issues**
- **Impact:** High (revenue interruption)
- **Likelihood:** Low
- **Mitigation:**
  - Use Stripe (trusted, reliable infrastructure)
  - Implement robust webhook retry logic
  - Monitor failed payments daily, proactive outreach
  - Build grace period (7 days) before service interruption

---

### Operational Risks

**Risk: Founder Burnout**
- **Impact:** Critical (project abandonment)
- **Likelihood:** Medium (solo founder, full-time development)
- **Mitigation:**
  - Set sustainable working hours (40-50 hours/week max)
  - Hire customer success help early (Month 6)
  - Build automation to reduce manual tasks
  - Take regular breaks, don't sacrifice health

**Risk: Support Volume Overwhelming Resources**
- **Impact:** Medium (customer satisfaction decline)
- **Likelihood:** Medium
- **Mitigation:**
  - Invest heavily in documentation and video tutorials
  - Build self-service knowledge base
  - Set clear response time expectations (24 hours)
  - Hire support help when ticket volume exceeds 10/day

**Risk: Infrastructure Cost Spiral**
- **Impact:** Medium (margin compression)
- **Likelihood:** Low
- **Mitigation:**
  - Implement cost monitoring alerts (AWS CloudWatch, Vercel dashboard)
  - Regular infrastructure audits (quarterly)
  - Optimize expensive operations (image processing, API calls)
  - Price Pro tier to cover heavier usage patterns

---

## Competitive Analysis

### Direct Competitors

**Generic Website Builders (Wix, Squarespace, Weebly)**
- **Strengths:** Brand recognition, easy to use, cheap ($12-30/month)
- **Weaknesses:** No sports-specific features, no API integrations, high effort to maintain schedules/rosters manually
- **Differentiation:** VolleyStack automates volleyball-specific workflows, 10x faster to maintain

**Sports Club Platforms (TeamSnap, SportsEngine)**
- **Strengths:** Feature-rich, established in market, mobile apps
- **Weaknesses:** Expensive ($200-500/month), complex, overkill for small clubs, focused on team management not public websites
- **Differentiation:** VolleyStack is 75% cheaper, focused on public-facing website needs, simpler

**Custom Development Agencies**
- **Strengths:** Fully customized solution, unique design
- **Weaknesses:** $5,000-15,000 upfront, ongoing maintenance costs, technical dependency on agency
- **Differentiation:** VolleyStack is 95% cheaper over 2 years, includes maintenance and hosting

---

### Competitive Advantages

1. **Volleyball-Specific:** Purpose-built for volleyball clubs, not generic sports
2. **API Integrations:** Automated data sync from league APIs
3. **Price Point:** $79/month hits sweet spot between DIY and enterprise
4. **Modern Tech Stack:** Performance and reliability typically reserved for enterprise
5. **Zero Technical Debt:** Continuous platform improvements benefit all clubs automatically

---

## Success Factors

### What Must Go Right

1. **Product-Market Fit:** Pilot clubs must validate that VolleyStack solves their core problems
2. **Operational Excellence:** Platform must be reliable (>99.9% uptime), fast (<2s page loads), and secure
3. **Customer Success:** Clubs must see value immediately, use platform weekly, renew after Year 1
4. **Efficient Acquisition:** CAC must remain <$150 to achieve profitability at target scale
5. **Founder Execution:** Consistent progress across development, sales, and support for 12+ months

### What Could Accelerate Success

1. **Partnership with Volleyball Ireland:** Official endorsement drives credibility and referrals
2. **Viral Referral Mechanism:** Clubs promote to competitor clubs, organic growth
3. **Media Coverage:** Feature in volleyball publications or local business news
4. **Multi-Sport Expansion:** Adjacent sports (basketball, netball) 10x addressable market
5. **White-Label Opportunity:** Regional associations license platform for their entire league

---

## Conclusion

VolleyStack addresses a clear market need with a technically sound solution and financially viable business model. The project is well-positioned for success given:

- **Validated problem:** Confirmed through interviews with 15 target clubs
- **Sustainable economics:** 96% gross margins, 2-month payback period
- **Technical feasibility:** Modern, proven tech stack with clear implementation path
- **Low initial risk:** Minimal upfront investment ($162-512), free infrastructure tiers for validation
- **Scalable foundation:** Architecture supports 5 to 500+ clubs without fundamental changes

**Recommendation:** Proceed with Phase 1 development, targeting pilot launch in Month 4. Validate assumptions with 5 pilot clubs before scaling to 50. Re-evaluate after pilot phase to confirm product-market fit and adjust strategy as needed.

**Next Steps:**
1. Finalize project name and register domain
2. Set up development environment (Week 1)
3. Begin Phase 1 implementation following technical roadmap
4. Recruit 5 pilot clubs (parallel to development, target by Week 8)