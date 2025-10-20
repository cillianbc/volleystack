# VolleyStack - Complete External Data Integration Summary

**Project:** VolleyStack Volleyball Club SaaS Platform  
**Last Updated:** October 2025  

---

## Executive Summary

VolleyStack integrates **THREE external data sources** to provide comprehensive, automated content:

| Data Source | Type | What It Provides | Value |
|-------------|------|------------------|-------|
| **League Standings API** | JSON | Team statistics, positions, records | Automatic standings tables |
| **Fixtures API** | JSON | Match schedules, venues, dates/times | No manual schedule entry needed |
| **News Feed** | RSS | Official volleyball news | Automatic authoritative content |

### Impact on Original Project Plan

**BEFORE (Original Assumptions):**
- ❌ Assumed: Manual schedule entry required
- ❌ Assumed: League standings only (if lucky)
- ❌ Assumed: News feed maybe available

**AFTER (Reality):**
- ✅ **Full fixtures API** - eliminates manual entry!
- ✅ **Complete standings** - with sub-leagues for Div 2/3
- ✅ **Official news feed** - easy RSS integration

**Net Result:**
- **Save 1 week** of development (no manual schedule entry UI)
- **Higher value proposition** (automatic data everywhere)
- **Lower maintenance burden** (clubs do less manual work)

---

## 1. League Standings & Fixtures API

### Source
- **Provider:** VIR Volleyball Standings (GitHub Pages)
- **Developer:** cillianbc (same person who built volleyballireland.com)
- **Base URL:** `https://cillianbc.github.io/volleyball-league-data/`
- **Authentication:** None required
- **Cost:** Free (static JSON files)

### Available Data

**Standings Endpoints (8 divisions):**
```
/current/mens-premier-division.json
/current/womens-premier-division.json
/current/mens-division-1.json
/current/womens-division-1.json
/current/division-2-men.json           ← Nested format (sub-leagues)
/current/division-3-men.json           ← Nested format
/current/wo-division-2-women.json      ← Nested format
/current/wo-division-3-women.json      ← Nested format
```

**Fixtures Endpoints (8 divisions):**
```
/fixtures/Men's%20Premier%20Division.json
/fixtures/Women's%20Premier%20Division.json
/fixtures/Men's%20Division%201.json
/fixtures/Women's%20Division%201.json
/fixtures/Men's%20Division%202.json
/fixtures/Men's%20Division%203.json
/fixtures/Women's%20Division%202.json
/fixtures/Women's%20Division%203.json
```

### Data Structures

**Two Standings Formats:**
1. **Flat** (Premier & Div 1): Direct `teams[]` array
2. **Nested** (Div 2 & 3): `subLeagues[]` with pools/groups

**Fixtures Format:**
- Single format across all divisions
- Fields: matchday, sub_league, date (DD/MM/YYYY), time, venue, home_team, away_team
- No unique IDs (must generate from composite key)

### Key Challenges

1. **Team Name Mismatch:** Fixtures vs standings use different suffixes
   - Solution: Fuzzy matching algorithm

2. **Nested Structure:** Div 2 & 3 have sub-leagues
   - Solution: Separate SubLeagues collection

3. **No Match Results:** Can only infer completion from dates
   - Solution: Date-based status + manual entry capability

4. **Date Format:** DD/MM/YYYY (European, not US!)
   - Solution: Parse carefully to avoid month/day confusion

### Sync Strategy

| Data Type | Frequency | Rationale |
|-----------|-----------|-----------|
| **Standings** | Every Sunday night | Changes after each match |
| **Fixtures** | Every Monday night | Rarely changes mid-season |

### Collections Required

**Section: Collections Required - Team Associations**

**Platform-level:**
- `league-divisions` - Division metadata and API URLs
- `sub-leagues` - Pools/groups for nested divisions
- `external-teams` - Teams from standings API
- `team-league-associations` - Links internal to external teams
  - **Unlinking behavior:** Preserves historical fixture and standings data
  - **Re-linking behavior:** Can link to different external team, retains old data
  - **Custom teams:** Internal teams can exist without external link

**Tenant-level:**
- `match-fixtures` - Synced fixtures
  - **Persistence:** Retained even if team unlinked from league
  - **Manual entry:** Can add fixtures for custom teams
- `league-standings-snapshots` - Historical tracking
  - **Snapshots preserved:** Even after unlinking
  - **Use case:** Historical performance analysis
- `team-league-associations` metadata stored here per tenant

## 2. Volleyball Ireland News Feed

### Source
- **Provider:** Volleyball Ireland
- **Feed URL:** `https://www.volleyballireland.com/feed/`
- **Website:** `https://www.volleyballireland.com/latest-news/`
- **Platform:** WordPress RSS 2.0
- **Authentication:** None required
- **Cost:** Free

### Available Data

**RSS Feed Includes:**
- Article title, excerpt, full content
- Featured images (high quality)
- Publication date, author
- Categories (High Performance, Events, Community)
- Link to original article

**Content Types:**
- Official announcements
- Tournament/event info
- National team updates
- Coaching appointments
- Community stories

### Key Considerations

**Copyright:**
- Always link to original article
- Show "via Volleyball Ireland" attribution
- Always include a canonical link back to original

**Update Frequency:**
- ~2-3 posts per week (estimated)
- Sync every 24 hours during season
- Cache images locally

### Collections Required

**Platform-level only:**
- `news-articles` - Shared across all clubs
  - Store: title, excerpt, sourceUrl, featuredImage
  - Track: publishedAt, categories, active status

### Display Components

2. **Dedicated News Page:** Full archive with filtering
3. **Admin Dashboard:** Relevant news for administrators


## 3. Complete Data Flow Architecture

### Data Ingestion Pipeline

**Section: Data Ingestion Pipeline - Sync Strategy**

Updated timing:
```markdown
### Sync Strategy

| Data Type | Frequency | Rationale |
|-----------|-----------|-----------|
| **Standings** | Nightly at 2:00 AM UTC | Changes after weekend matches (Sunday night source update) |
| **Fixtures** | Nightly at 2:00 AM UTC | Rarely changes mid-season (Monday night source updates) |
| **News Feed** | Not implemented in pilot | Manual news posts by clubs sufficient initially |

**Rationale for Nightly Sync:**
- Source APIs update weekly, not continuously
- Reduces unnecessary API load (100+ calls/day → 1 call/day)
- Off-peak timing minimizes impact on user experience
- Fresh data ready by morning when admins/members check sites
- Simplifies error handling and monitoring
```

```
┌─────────────────────────────────────────────────────────┐
│                  EXTERNAL DATA SOURCES                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌───────────────┐  ┌───────────────┐  ┌────────────┐ │
│  │   Standings   │  │   Fixtures    │  │  News Feed │ │
│  │   API (x8)    │  │   API (x8)    │  │    (RSS)   │ │
│  └───────┬───────┘  └───────┬───────┘  └──────┬─────┘ │
│          │                   │                  │       │
└──────────┼───────────────────┼──────────────────┼───────┘
           │                   │                  │
           │ Every Sunday night       │ Every Monday night       │ Every 24hrs
           │                   │                  │
           ▼                   ▼                  ▼
┌─────────────────────────────────────────────────────────┐
│              BACKGROUND SYNC JOBS (Payload)             │
├─────────────────────────────────────────────────────────┤
│  ┌────────────────┐  ┌──────────────┐  ┌─────────────┐ │
│  │ syncStandings  │  │ syncFixtures │  │  syncNews   │ │
│  │                │  │              │  │             │ │
│  │ • Parse format │  │ • Parse JSON │  │ • Parse RSS │ │
│  │ • Upsert teams │  │ • Fuzzy match│  │ • Cache img │ │
│  │ • Create snap  │  │ • Link teams │  │ • Sanitize  │ │
│  └────────┬───────┘  └──────┬───────┘  └──────┬──────┘ │
└───────────┼──────────────────┼──────────────────┼────────┘
            │                  │                  │
            ▼                  ▼                  ▼
┌─────────────────────────────────────────────────────────┐
│                  POSTGRESQL DATABASE                    │
├─────────────────────────────────────────────────────────┤
│  Platform-level:                                        │
│  • league_divisions       • news_articles               │
│  • sub_leagues            • external_teams              │
│                                                         │
│  Tenant-level:                                          │
│  • match_fixtures         • league_standings_snapshots  │
│  • team_league_associations                             │
└───────────────────────────┬─────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────┐
│                  FRONTEND DISPLAY                       │
├─────────────────────────────────────────────────────────         │
└─────────────────────────────────────────────────────────┘
```

### Monitoring Strategy

**Health Checks:**
- Monitor each sync job success/failure rate
- Alert on 3+ consecutive failures
- Track API response times
- Monitor data freshness (last sync timestamp)

**Dashboards:**
- Admin dashboard shows sync status for all data sources
- Display "Last updated" timestamps on public pages
- Show warning if data is stale (>24 hours)

---

## 5. Error Handling & Resilience

### Circuit Breaker Pattern

**For all external API calls:**

```
If API fails 5 times consecutively:
  → Open circuit (stop calling API)
  → Use cached data
  → Try again after 5 minutes
  
If next call succeeds:
  → Close circuit (resume normal operation)
```

### Graceful Degradation

| Failure Scenario | User Experience |
|------------------|-----------------|
| Standings API down | Show cached standings + "Last updated X hours ago" |
| Fixtures API down | Show cached fixtures + notice |
| News feed down | Hide news widget OR show cached articles |
| Image download fails | Show placeholder image |
| Parse error | Skip sync cycle, log error, try again next cycle |

---

## 8. Value Proposition Enhancement

### For Clubs (End Users)

**Before VolleyStack:**
- Club admin manually enters match schedule (30+ fixtures)
- No standings data (have to check league website repeatedly)
- Manual roster management in spreadsheets
- No centralized communication platform

**After VolleyStack:**
- ✅ Schedule auto-populates from fixtures API (nightly sync)
- ✅ League table updates automatically overnight
- ✅ CSV roster import (5 minutes vs 2 hours manual entry)
- ✅ "Just link your team" - everything else is automatic
- ✅ Custom teams supported for development squads

**Time Saved Per Club:**
- Schedule entry: ~2 hours per season
- Weekly schedule checking: ~30 minutes per week × 20 weeks = 10 hours
- Roster data entry: ~1.5 hours per season
- **Total: ~13-15 hours per club per season**

**Additional Benefits:**
- No frustration with team name mismatches (CSV template has exact names)
- Historical data preserved (even if team changes divisions)
- Admin can manage everything in one place
- Parents/members always see current information


### For VolleyStack (Platform)

**Competitive Advantages:**
1. **Only platform** with full Volleyball Ireland data integration
2. **Automatic everything** - clubs do minimal manual work
3. **Always current** - no stale schedules or standings
4. **Professional appearance** - shows clubs are "plugged in"
5. **Lower support burden** - less manual data entry = fewer errors

**Marketing Messages:**
- "Your schedule updates automatically"
- "League tables refresh every 30 minutes"
- "National news on every club's homepage"
- "Set it and forget it"

---

## 9. Risks & Mitigation

### API Dependency Risks

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| API structure changes | Medium | High | Version detection, alert on parse errors |
| API goes offline | Low | High | Circuit breaker, cached data, manual fallback |
| Team name changes | Medium | Medium | Fuzzy matching, manual mapping table |
| Feed removed | Low | Medium | Contact VLY for advance notice, backup plan |

### Mitigation Strategies

1. **Version Detection:** Check API structure on each sync, alert if changed
2. **Graceful Degradation:** Always show cached data if sync fails
3. **Manual Override:** Allow clubs to manually enter data if API fails
4. **Relationship Management:** Maintain contact with cillianbc (API maintainer)
5. **Documentation:** Document API structures thoroughly for quick debugging

## 5. New Section: Payment & Contract Management

**Add to project_brief.md after "Business Model" section:**
```markdown
## Payment & Contract Management

### Contract Terms

**12-Month Commitment:**
- All clubs sign 12-month contracts (€420 Starter, €948 Pro)
- Invoiced monthly via Stripe
- No in-app cancellation option
- Builds stable, predictable revenue
- Aligns with volleyball season cycle (September-June)

**Payment Terms:**
- Net 14 days from invoice date
- Automated payment reminders via Stripe
- Manual follow-up for overdue accounts

### Failed Payment Handling

**Day 0-7: Automated Reminders**
- Stripe sends automatic payment reminders
- 3 retry attempts on Days 1, 3, 5
- No service interruption

**Day 7: Personal Outreach**
- Founder calls club admin
- Understand reason for non-payment
- Offer payment plan if needed
- Relationship-building opportunity

**Day 14: Warning Banner**
- Site shows banner: "⚠️ Payment overdue. Site will be disabled in 7 days."
- Admin panel accessible, editing disabled
- Email reminder sent to admin

**Day 21: Service Suspension**
- Site replaced with holding page:
  - "This club's subscription is suspended due to payment issues."
  - "Club admins: Please contact support@volleystack.com"
- Admin panel login disabled
- Data preserved (not deleted)

**Reactivation:**
- Upon payment receipt, site reactivated within 2 hours
- No data loss
- 1-month grace period before requiring new 12-month commitment

### Cancellation Policy

**Mid-Contract Cancellation:**
- No refunds for remaining contract period
- Service continues until contract end date
- Option to downgrade (Pro → Starter) if financial hardship
- Case-by-case decisions during pilot phase

**End-of-Contract Renewal:**
- 30-day notice required for non-renewal
- Automatic renewal unless cancelled
- Founder reaches out 60 days before renewal to discuss satisfaction

### Pro Tier Features

**Included in Pro (€79/month):**
- Custom domain (clubname.com)
- Unlimited teams
- 50GB storage (vs 10GB Starter)
- Priority support (24-hour SLA vs 48-hour)
- Advanced analytics (future)
- Custom CSS options (future)

**Domain Setup Process:**
- Admin enters desired domain in settings
- System generates TXT verification code
- Admin adds DNS records (A, CNAME, TXT)
- Founder verifies DNS setup manually (pilot phase)
- SSL certificate auto-provisions within 48 hours
- Domain goes live once SSL ready
- Documentation provided, phone support available

## 11. Next Steps

### Immediate (Before Development)

1. **Verify API Stability**
   - [✅] Test all 16 endpoints (8 standings + 8 fixtures)
   - [✅] Monitor for 1 week to understand update patterns
   - [✅] Contact cillianbc to confirm API is stable/supported

2. **Seek Permission**
   - [✅] Contact Volleyball Ireland re: news feed syndication
   - [✅] Confirm OK to display fully with canonical

## Summary

### What We Discovered

The Volleyball Ireland ecosystem provides **complete, free data APIs**:

1. ✅ **League Standings** - More complex (nested format) but complete
2. ✅ **Match Fixtures** - Unexpected bonus, eliminates manual entry!
3. ✅ **Official News** - Standard RSS, easy integration

### Impact on Project

**Complexity:** Slightly higher than originally estimated
- Handling two standings formats
- Team name fuzzy matching
- Three separate sync jobs

**Value:** MUCH higher than originally expected
- Automatic fixtures (huge!)
- Complete standings with sub-leagues
- Official news content
- Professional, "plugged-in" appearance

**Timeline:** Net neutral (saves time on manual entry UI)

**ROI:** Excellent
- ~90 hours development
- Saves clubs 20+ hours per season
- Significantly enhances value proposition
- Differentiates from all competitors

### Recommendation

**Proceed with all three integrations in Weeks 6-7.**

The development effort is justified by the massive value delivered to clubs and the competitive advantage gained. No other volleyball club platform offers this level of automated data integration.