# Partner Qualification Checklist
## Phase 1: Initial Screening (30-Minute Discovery Call)

**Purpose**: Quickly eliminate providers who aren't technically compatible with JobNimbus Financing Platform

**When to Use**: First conversation with any potential financing provider

---

## Pre-Call Research (5 minutes)

- [ ] Company website reviewed
- [ ] Basic product offerings identified
- [ ] LinkedIn company profile checked
- [ ] Any existing customer reviews found

---

## Screening Questionnaire

### Business Model Fit

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| Do you offer contractor/merchant financing programs? | Yes | ✅ Yes | If no, end call |
| Is financing provided to homeowners/end consumers? | Yes | ✅ Yes | Must be B2B2C model |
| Do you support home improvement projects? | Yes | ⚠️ Preferred | Industry-specific OK |
| What industries do you serve? | [List] | ❌ No | Document for targeting |
| What is your geographic coverage? | US-based minimum | ⚠️ Preferred | State restrictions OK |

### Merchant Onboarding

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| Can contractors sign up as merchants with your service? | Yes | ✅ Yes | Must allow new merchants |
| Is there an API for merchant signup/onboarding? | Yes/No | ❌ No | Manual portal OK for Phase 1 |
| What's your merchant approval process? | [Description] | ❌ No | Document timeline |
| Average time to approve new merchants? | <30 days | ⚠️ Preferred | >60 days = risk |
| Merchant rejection rate? | <20% | ⚠️ Preferred | High rejection = poor UX |
| Do you require exclusivity from merchants? | No | ✅ Yes | Multi-provider required |

### Consumer Financing Capabilities

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| Can consumers apply for loans via URL/link? | Yes | ✅ Yes | Redirect model required |
| Do you support pre-qualification (soft credit check)? | Yes/No | ❌ No | Nice to have, not required |
| Can loan applications be created via API? | Yes | ✅ Yes | Must have programmatic access |
| Can customer data be pre-filled in application? | Yes/No | ❌ No | UX benefit only |
| Do you support SMS-based application invites? | Yes/No | ❌ No | Nice to have |
| Loan amount range supported? | $1K - $100K+ | ⚠️ Preferred | Document min/max |

### Loan Products

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| What loan types do you offer? | [List all types] | ❌ No | Variety is a plus |
| Interest rate range? | Competitive with market | ⚠️ Preferred | Document range |
| Loan term options? | 12-120+ months | ⚠️ Preferred | Flexibility preferred |
| Do you offer promotional products (0% APR, deferred interest)? | Yes/No | ❌ No | Competitive advantage |
| Average approval rate? | >50% | ⚠️ Preferred | Low approval = poor UX |
| Time to approval decision? | <24 hours | ⚠️ Preferred | Instant preferred |

### Technical Capabilities

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| Do you have a REST API? | Yes | ✅ Yes | SOAP/XML not supported |
| Do you have API documentation? | Yes/No | ⚠️ Strongly Preferred | Will request in Phase 2 |
| Do you support webhooks for real-time status updates? | Yes/No | ⚠️ Strongly Preferred | Polling OK but suboptimal |
| Authentication method? | API Key/OAuth/Basic | ❌ No | Any method OK |
| Do you have a sandbox/test environment? | Yes/No | ⚠️ Preferred | Required for integration |
| Can you provide test credentials? | Yes/No | ⚠️ Preferred | Required for development |

### Security & Compliance

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| Are you SOC 2 certified? | Yes/No | ⚠️ Preferred | Required for enterprise |
| PCI DSS compliant (if handling card data)? | Yes/No | ⚠️ If applicable | Required if storing cards |
| Do you have a published privacy policy? | Yes | ✅ Yes | Regulatory requirement |
| GDPR/CCPA compliant? | Yes | ✅ Yes | US/EU customers |
| Do you perform background checks on merchants? | Yes/No | ❌ No | Risk management plus |
| What data do you share back with partners? | [Details] | ❌ No | Document limitations |

### Support & Partnership

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| Do you offer partner/integration support? | Yes | ⚠️ Strongly Preferred | Critical for success |
| Support response time SLA? | <24 hours | ⚠️ Preferred | Document SLA |
| Can you provide a dedicated integration engineer? | Yes/No | ❌ No | Major plus if yes |
| Do you have other platform integrations? | [List] | ❌ No | Experience is a plus |
| Are you interested in co-marketing opportunities? | Yes/No | ❌ No | Partnership depth signal |

### Business Terms (High-Level)

| Question | Required Answer | Deal Breaker? | Notes |
|----------|----------------|---------------|-------|
| Revenue share or referral fee structure? | [Describe model] | ❌ No | Defer to commercial |
| Merchant discount rate (MDR) range? | [Range] | ❌ No | Competitive analysis |
| Any exclusivity requirements from JobNimbus? | No | ✅ Yes | Must allow multi-provider |
| Average merchant fees? | Transparent | ⚠️ Preferred | Hidden fees = red flag |
| Contractor payment timeline? | Net 30 or better | ⚠️ Preferred | Cash flow impact |

---

## Scoring & Decision

### Calculate Total Score

**Required Criteria (10 questions marked ✅):**
- [ ] All 10 required = Yes

**Preferred Criteria (16 questions marked ⚠️):**
- Count "Yes" answers: _____ / 16

### Decision Matrix

| Required Score | Preferred Score | Decision | Next Steps |
|----------------|-----------------|----------|------------|
| 10/10 | 12-16 | ✅ **Green Light** | Immediately send Phase 2 RFI |
| 10/10 | 8-11 | ⚠️ **Yellow Light** | Document gaps, proceed with caution, send RFI |
| 10/10 | 0-7 | ⚠️ **Yellow Light - Risk** | Major gaps, consider deferring to Phase 3 |
| <10/10 | Any | ❌ **Red Light** | Reject or revisit in 6-12 months |

---

## Post-Call Actions

### Green Light ✅
- [ ] Send thank you email within 24 hours
- [ ] Send Phase 2 Technical Integration RFI immediately
- [ ] Add to provider tracking spreadsheet (Tier 1 candidate)
- [ ] Schedule follow-up in 2 weeks
- [ ] Add to Slack #financing-partners channel

### Yellow Light ⚠️
- [ ] Document specific gaps or concerns
- [ ] Determine if gaps are addressable
- [ ] Send Phase 2 RFI with note about gaps
- [ ] Add to provider tracking spreadsheet (Tier 2 candidate)
- [ ] Schedule follow-up in 2 weeks

### Red Light ❌
- [ ] Send polite rejection email
- [ ] Document reason for rejection
- [ ] Add to "Revisit Later" list
- [ ] Set calendar reminder for 6 months (optional)

---

## Notes Template

**Provider**: _______________________
**Contact**: _______________________
**Date**: _______________________
**Interviewed By**: _______________________

**Key Strengths**:
- 
- 
- 

**Concerns/Gaps**:
- 
- 
- 

**Unique Features**:
- 
- 
- 

**Decision**: ✅ Green Light | ⚠️ Yellow Light | ❌ Red Light

**Next Steps**:
- 
- 
- 

**Internal Notes** (do not share with provider):
- 
- 
- 

---

## Email Templates

### Green Light - Send RFI

```
Subject: JobNimbus Financing Partnership - Next Steps

Hi [Name],

Thank you for the productive conversation today about [Provider Name]'s financing solutions. We're excited about the potential partnership!

Based on our discussion, I'd like to move forward with a technical evaluation. Attached is our Technical Integration RFI that outlines the API documentation and information we'll need to assess integration feasibility.

Could you please provide responses by [Date - 2 weeks from today]? We're targeting [Month] for our next integration wave and would love to include [Provider Name] if we can align on technical requirements.

Next steps:
1. Review attached Technical Integration RFI
2. Provide requested API documentation
3. Schedule technical deep-dive call (after RFI review)

Looking forward to working together!

Best,
[Your Name]
Product Manager, Financing Platform
JobNimbus
```

### Yellow Light - Send RFI with Gaps

```
Subject: JobNimbus Financing Partnership - Information Request

Hi [Name],

Thanks for taking the time to discuss [Provider Name]'s capabilities today. We see potential for partnership, though we did identify a few areas where we'd like more information.

**Gaps/Questions**:
- [List specific gaps from call]
- [Another gap]

Despite these questions, we'd like to proceed with a technical evaluation to better understand your API capabilities. Attached is our Technical Integration RFI.

Could you please:
1. Address the gaps/questions above
2. Complete the Technical Integration RFI
3. Provide requested API documentation

Timeline: [Date - 2 weeks from today]

We'll review your responses and determine next steps from there.

Best,
[Your Name]
```

### Red Light - Polite Rejection

```
Subject: JobNimbus Financing Partnership - Current Fit

Hi [Name],

Thank you for your time today discussing [Provider Name]'s financing solutions. We appreciate your interest in partnering with JobNimbus.

After reviewing our current requirements, we've determined that [Provider Name] isn't the right fit for our immediate integration roadmap, primarily due to [brief reason - e.g., "lack of API capabilities" or "geographic limitations"].

We'll keep [Provider Name] in mind for future phases as our platform evolves. I've added a reminder to revisit this conversation in [6 months/1 year].

Thank you again for your time, and best of luck with your business!

Best,
[Your Name]
```

---

## Appendix: Quick Reference

### Deal Breakers (Auto-Reject if "No")
1. Contractor/merchant financing offering
2. Consumer financing (not just B2B)
3. Can sign up new merchants
4. Consumer application via URL/link
5. Has REST API
6. API for creating applications
7. No exclusivity required
8. Has privacy policy
9. GDPR/CCPA compliant

### Strong Positive Signals
- Has extensive API documentation
- Offers sandbox environment
- Provides dedicated integration support
- Has other platform integrations
- Offers promotional products (0% APR)
- Webhook support for real-time updates
- Pre-qualification capability
- Fast approval times (<1 hour)

### Red Flags
- Requires exclusivity
- No API (manual only)
- High merchant rejection rate (>30%)
- Slow approval times (>48 hours)
- No test environment
- Poor/no API documentation
- No webhook support AND no polling API
- Excessive fees not disclosed upfront
- Geographic limitations (single state only)

---

**Document Version**: 1.0
**Last Updated**: 2025-01-23
**Owner**: Financing Platform Team

