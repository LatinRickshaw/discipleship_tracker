# Kingdom Discipleship Tracker - Implementation Plan

## Executive Summary

The Kingdom Discipleship Tracker is a Christian discipleship app designed to help believers grow in Christ-likeness through relationship-centered accountability. The app measures spiritual growth across four Kingdom Pillars while avoiding legalism by focusing on nurturing presence with God rather than behavioral compliance.

## Core Vision

**Theological Foundation:**
- Theosis (Christ-likeness) through transformation, not moralism
- Kingdom Mission: disciples as agents of restoration and justice
- Fractal Design: reflecting God's communal, loving nature at every level
- Fellowship-Dependent: inoperable without one-on-one discipleship relationships

**Key Design Principles:**
1. **Relationship-Centered**: App enhances (never replaces) human discipleship relationships
2. **Baseline Tracking**: Compare against personal growth trajectory, not impossible ideals
3. **Anti-Legalism**: Gentle theological guardrails redirect users away from rule-based spirituality
4. **Church-Integrated**: Explicitly reinforces fellowship, community, and embodied church participation
5. **Geodolite Approach**: Continuous calibration between theological direction and daily practice

## Critical User Insight from Transcripts

From the conversation analysis, one concern needs addressing before proceeding:

**The "Ideal Standard" Question:**
- Original gemini.md envisions tracking against a "God-like state" or ideal
- Darren's transcript challenge: comparing against an unreachable ideal is demoralizing and unbiblical
- Resolution needed: How do we balance having theological guardrails (preventing drift) with personal baseline tracking (measuring individual growth)?

**Proposed Approach:**
- **Theological Compass** (not "ideal"): Define healthy Kingdom discipleship principles that guide without crushing
- **Personal Baseline**: First 2 weeks establish user's starting point across four pillars
- **Growth Trajectory**: Track personal improvement over time (the stairway metaphor)
- **Guardrails**: AI detects drift toward legalism, nationalism, or theological error and gently redirects

## Four Kingdom Pillars

Based on gemini.md extensive analysis:

1. **Love for God / Inner Life**: Prayer, Scripture engagement, virtue acquisition
2. **New Humanity**: Fellowship, vulnerability, embodied community
3. **Justice & Reconciliation**: Shalom action, bridge-building, local service
4. **Culture Making & Vocation**: Kingdom witness in work, creativity, mission

## High-Level MVP Architecture

### MVP Scope: "Core Loop in 4-6 Weeks" ✅

**What's IN the MVP (Non-Negotiables):**
- ✅ User authentication and profiles (Disciple + Discipler roles)
- ✅ Simple daily check-in: **TEXT INPUT ONLY** for "crucible moments"
- ✅ **Four-pillar radar chart visualization** showing personal baseline
- ✅ **Relational check-in gate**: Discipler confirms meeting to unlock analysis
- ✅ **AI theological analysis with RAG**: Compares user input against theological markdown template
- ✅ Basic gap analysis display with growth opportunities

**What's OUT of MVP (Deferred to Phase 2+):**
- ❌ Speech-to-text (adds 2-3 weeks, can add later)
- ❌ Baseline calculation (2-week learning period) - MVP shows immediate feedback
- ❌ Weekly goal setting interface (handle in-person for now)
- ❌ Kingdom Replicability Score (KRS) for measuring multiplication
- ❌ Daily narrative stories (Sean of the South style)
- ❌ Complex metric logging (hours, scales, tallies)
- ❌ Resource recommendation engine
- ❌ Historical growth tracking / stairway visualization
- ❌ Multi-church deployment infrastructure
- ❌ Mobile optimization (desktop-first)

### Technical Stack Recommendations

**Frontend:**
- Next.js or React SPA for responsive UI
- Chart.js or Recharts for radar chart visualization
- Tailwind CSS for beautiful, accessible design

**Backend:**
- Node.js + Express OR Supabase (rapid MVP with auth built-in)
- PostgreSQL database
- RESTful API or GraphQL

**AI Integration:**
- **RAG Service**: Use Pinecone or Weaviate vector database for theological content
- **LLM**: OpenAI GPT-4 or Anthropic Claude for semantic analysis
- **Speech-to-Text**: Whisper API or Deepgram
- **Theological Template**: Markdown files defining healthy Kingdom discipleship patterns

**Hosting:**
- Vercel (frontend) + Railway/Render (backend) for quick deployment
- OR all-in-one Supabase approach

## Revised Implementation Phases (4-6 Week MVP)

### Phase 1: Core Infrastructure (Week 1)
**Goal:** Get authentication, database, and basic UI scaffolding working

**Deliverables:**
- User authentication (Supabase Auth for speed)
- Role selection: Disciple or Discipler
- Database schema: Users, CheckIns, Relationships
- Basic routing and protected pages
- Minimal onboarding (role selection + brief explanation)

**Critical Files:**
- `/supabase/schema.sql` - Database tables and relationships
- `/frontend/pages/index.jsx` - Landing/login page
- `/frontend/pages/onboarding.jsx` - Role selection
- `/frontend/pages/dashboard.jsx` - Main dashboard shell
- `/frontend/lib/supabaseClient.js` - Supabase connection
- `/frontend/contexts/AuthContext.jsx` - Authentication state

**Verification:**
- ✅ User can sign up with email/password
- ✅ User selects role (Disciple or Discipler)
- ✅ Login redirects to appropriate dashboard
- ✅ Database tables created and tested

---

### Phase 2: Disciple Core Loop (Week 2)
**Goal:** Enable disciple to submit check-ins and see radar chart

**Deliverables:**
- Daily check-in form (text area for crucible moment)
- Four-pillar questionnaire (simple 1-5 scales per pillar for MVP)
- Radar chart visualization using Chart.js
- Check-in history view (list of past reflections)

**Critical Files:**
- `/frontend/components/CheckInForm.jsx` - Daily reflection input
- `/frontend/components/PillarQuestionnaire.jsx` - 4 simple questions (1-5 scale)
- `/frontend/components/RadarChart.jsx` - Chart.js radar visualization
- `/frontend/pages/disciple/checkin.jsx` - Check-in page
- `/backend/api/checkins.js` - API route for saving check-ins

**Verification:**
- ✅ Disciple can submit daily text reflection
- ✅ Disciple rates themselves 1-5 on each pillar
- ✅ Radar chart displays current scores visually
- ✅ Past check-ins visible in history

---

### Phase 3: Theological RAG System (Week 3)
**Goal:** Create AI analysis comparing reflections to theological compass

**Deliverables:**
- **Darren creates** initial theological markdown templates (work in parallel)
- Vector database setup (Pinecone free tier or Weaviate)
- Embedding generation from markdown content
- AI service: compare check-in text to theological patterns
- Semantic scoring across four pillars
- Gap analysis display: show areas for growth + gentle redirects

**Critical Files:**
- `/theological-compass/kingdom-pillars.md` - **Darren writes this**
- `/theological-compass/inner-life.md` - **Darren writes this**
- `/theological-compass/new-humanity.md` - **Darren writes this**
- `/theological-compass/justice.md` - **Darren writes this**
- `/theological-compass/culture-making.md` - **Darren writes this**
- `/theological-compass/anti-legalism.md` - **Darren writes this**
- `/backend/services/ragService.js` - Vector DB + embeddings
- `/backend/services/aiAnalysis.js` - OpenAI/Claude API integration
- `/backend/api/analysis.js` - Gap analysis endpoint
- `/frontend/components/GapAnalysis.jsx` - Display growth opportunities

**Verification:**
- ✅ Theological markdown embedded in vector database
- ✅ AI analyzes user reflection and compares to theological patterns
- ✅ Gap analysis shows specific growth areas per pillar
- ✅ System detects legalistic language and gently redirects

---

### Phase 4: Relational Gate (Week 4)
**Goal:** Enable discipler relationship and gate analysis behind meeting confirmation

**Deliverables:**
- Disciple can invite/request discipler by email
- Discipler dashboard showing assigned disciples
- Crucible moment log (discipler views recent check-ins)
- Relational check-in confirmation button
- Gate logic: "Gap Analysis" tab locked until discipler confirms
- Email notification when disciple submits check-in

**Critical Files:**
- `/backend/models/Relationship.js` - Discipler-Disciple pairing
- `/backend/api/relationships.js` - Invite/accept relationship
- `/frontend/pages/discipler/dashboard.jsx` - Discipler view
- `/frontend/pages/discipler/[discipleId].jsx` - Individual disciple view
- `/frontend/components/RelationalGate.jsx` - Lock/unlock UI
- `/backend/api/checkin-confirmation.js` - Meeting confirmation endpoint

**Verification:**
- ✅ Disciple can invite discipler to relationship
- ✅ Discipler sees list of assigned disciples
- ✅ Discipler can read disciple's recent reflections
- ✅ "Gap Analysis" is locked/grayed out until meeting confirmed
- ✅ After confirmation, disciple can view AI analysis

---

### Phase 5: Polish & Test (Weeks 5-6)
**Goal:** Refine UX, fix bugs, prepare for alpha testing

**Deliverables:**
- UI/UX improvements (better typography, colors, spacing)
- Help text and tooltips explaining features
- Error handling and loading states
- Simple "About" page explaining the four pillars
- Deploy to production (Vercel + Supabase)
- Recruit 3-5 alpha testers (2-3 disciple/discipler pairs)
- Collect feedback and iterate

**Critical Files:**
- `/frontend/components/HelpTooltip.jsx` - Inline help
- `/frontend/pages/about.jsx` - Explain four pillars
- `/frontend/styles/globals.css` - Polish styling
- `/docs/alpha-testing-guide.md` - Instructions for testers

**Verification:**
- ✅ App deployed and accessible online
- ✅ 3-5 alpha users successfully complete full flow
- ✅ No critical bugs blocking core features
- ✅ Users report radar chart is encouraging
- ✅ Relational gate prevents "solo Christianity"

---

## Critical Path (4-6 Week MVP)

**Must-Have Sequence:**
1. ✅ **Phase 1 (Week 1)**: Authentication + database schema - BLOCKER for everything else
2. ✅ **Phase 2 (Week 2)**: Check-in form + radar chart - Core user experience
3. ✅ **Darren's Work (Parallel with Phase 2)**: Write theological markdown - BLOCKER for Phase 3
4. ✅ **Phase 3 (Week 3)**: RAG + AI analysis - Core differentiation and value
5. ✅ **Phase 4 (Week 4)**: Relational gate - Prevents self-help misuse
6. ✅ **Phase 5 (Weeks 5-6)**: Polish + alpha testing - Validation

**Can Be Simplified for MVP:**
- Self-rating scales (1-5) instead of complex AI-only scoring (Phase 2)
- Simple text display for gap analysis instead of fancy UI (Phase 3)
- Email invites instead of complex relationship request flows (Phase 4)
- Manual pillar scores for MVP; AI enhances but doesn't replace user input

**Deferred to Post-MVP:**
- Speech-to-text input
- 2-week baseline calculation
- Historical growth tracking
- Goal-setting interface
- KRS multiplication scores
- Daily narrative stories
- Mobile optimization

## Resource Requirements (Revised for 4-6 Week MVP)

**Development Time:**
- **4-6 weeks with Christian as solo developer** (using AI acceleration)
- Assumes ~20-30 hours/week effort (3-4 hours/weekday + weekend work)
- Darren's parallel work: ~10-15 hours writing theological markdown (Week 2-3)

**AI Services (Monthly Cost for Alpha):**
- OpenAI API: ~£30-50/month (5-10 alpha users, light usage)
- Pinecone Vector DB: FREE tier (sufficient for <100K vectors)
- Total AI costs: ~£40/month during alpha

**Hosting (Monthly Cost):**
- Vercel: FREE tier (hobby plan sufficient for alpha)
- Supabase: FREE tier (500MB database, 2GB file storage)
- Total hosting costs: **£0/month during alpha**

**Total MVP Budget: ~£200** (2 months of AI API costs while building + testing)

**Post-MVP Costs (if scaling):**
- Pinecone: ~£70/month (paid tier for production)
- Supabase: ~£25/month (Pro tier for production)
- OpenAI: ~£100-200/month (50-100 active users)
- **Total: ~£200-300/month** for 100 active users

## Theological Markdown Template Structure

The "theological compass" (not "ideal") should be organized as:

```
/theological-compass/
├── kingdom-pillars.md          # Core definition of four pillars
├── inner-life-patterns.md      # Healthy prayer/Scripture engagement
├── new-humanity-patterns.md    # Fellowship, vulnerability, community
├── justice-patterns.md         # Shalom action, bridge-building
├── culture-making-patterns.md  # Vocation, creativity, mission
├── anti-legalism.md           # Red flags: rule-obsession, shame, performance
├── anti-nationalism.md        # Red flags: political idolatry, tribalism
└── crucible-wisdom.md         # Examples of healthy reflections
```

Each file uses semantic markup for RAG embedding:
- **Healthy Pattern**: Descriptive language for virtuous behavior
- **Warning Sign**: Language indicating drift toward error
- **Gentle Redirect**: Suggested reframing when drift detected

## Questions ANSWERED ✅

Based on your responses:

1. ✅ **Theological Compass + Personal Baseline**: Using RAG guardrails to detect drift while tracking personal growth
2. ✅ **MVP Non-Negotiables**: AI theological analysis, Relational gate, Four-pillar radar chart
3. ✅ **Theological Content**: Darren will write the initial markdown templates
4. ✅ **Timeline**: 4-6 weeks for bare bones MVP
5. ✅ **Tech Stack**: Supabase all-in-one approach for speed

## Remaining Open Questions

1. **Alpha Testers**: Do you have 2-3 discipler/disciple pairs who could alpha test in weeks 5-6? Ideally people from HBC who understand the vision.

2. **Theological Markdown Timing**: Can you commit to writing the 6 theological markdown files during Week 2-3? This is a **hard blocker** for Phase 3. Each file should be 500-1500 words describing healthy patterns and warning signs.

3. **Project Name**: "Discipleship Tracker" is the working title. Do you want a more specific name? (e.g., "Kingdom Tracker", "Theosis App", "Fractal Discipleship", etc.)

4. **Church Affiliation**: Should the app be branded as an HBC tool initially, or keep it generic/non-denominational from the start?

## Next Steps (Immediate Actions)

### This Week:
1. ✅ **Review and approve this plan** - Christian awaits Darren's final sign-off
2. ✅ **Answer remaining open questions** - Alpha testers, theological markdown timing, naming
3. ✅ **Set up project repository** - Initialize Git repo, create folder structure
4. ✅ **Set up Jira board** - Create epics for Phases 1-5 with task breakdown
5. ✅ **Christian begins Phase 1** - Supabase setup, authentication, database schema

### Week 2-3 (Darren's Critical Work):
- 📝 **Write theological markdown files** (10-15 hours total):
  - `kingdom-pillars.md` - Overview of four pillars (500 words)
  - `inner-life.md` - Prayer, Scripture, virtue patterns (1000 words)
  - `new-humanity.md` - Fellowship, vulnerability, community (1000 words)
  - `justice.md` - Shalom action, bridge-building, reconciliation (1000 words)
  - `culture-making.md` - Vocation, creativity, Kingdom witness (1000 words)
  - `anti-legalism.md` - Warning signs and gentle redirects (800 words)

### Ongoing:
- 🔄 **Weekly check-ins** - 30-minute calls to review progress and blockers
- 🔄 **Daily async updates** - Quick Slack/WhatsApp messages on progress
- 🔄 **Theological review cycles** - Christian shares AI analysis output, Darren validates theology

## Success Criteria for MVP Launch (Revised for 4-6 Weeks)

### Functional Success:
- ✅ 2-3 disciple/discipler pairs successfully use the app for 2 weeks
- ✅ AI analysis completes without errors and returns relevant growth suggestions
- ✅ Relational gate successfully blocks gap analysis until meeting confirmed
- ✅ Radar chart renders correctly and updates after check-ins
- ✅ No data loss or critical bugs

### Theological Success:
- ✅ AI detects legalistic language in test cases (manual validation by Darren)
- ✅ Gap analysis feedback feels encouraging, not condemning
- ✅ Alpha testers confirm app enhances (not replaces) discipleship relationships
- ✅ Zero reports of feeling judged, shamed, or compared to unrealistic standards

### Learning Success:
- ✅ Identify 3-5 key improvements for next iteration
- ✅ Validate or invalidate core assumptions (relational gate, AI analysis value, pillar scoring)
- ✅ Gather feedback on what features are most/least valuable
- ✅ Determine if theological markdown needs expansion or refinement

---

## Summary: What We're Building

**A 4-6 week MVP discipleship tracker that:**
1. Helps disciples reflect daily on their spiritual growth across four Kingdom pillars
2. Uses AI to compare reflections against healthy theological patterns (not impossible ideals)
3. Visualizes growth with an encouraging radar chart showing personal trajectory
4. Prevents "solo Christianity" by gating analysis behind discipler confirmation of in-person meetings
5. Gently redirects users away from legalism, nationalism, and other theological drift
6. Strengthens (never replaces) one-on-one discipleship relationships and church community

**The geodolite metaphor:** Like tunneling from two directions, the app keeps theological truth and daily practice continuously aligned through weekly relational check-ins.

---

**Document Version:** 2.0 (Revised for 4-6 week timeline)
**Created:** 2026-01-12
**Last Updated:** 2026-01-12
**Status:** Ready for approval pending remaining open questions
