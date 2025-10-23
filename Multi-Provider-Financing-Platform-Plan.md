# Multi-Provider Financing Platform
## Implementation Plan

**Last Updated**: 2025-01-23  
**Owner**: Product Team - Financing Platform  
**Status**: Phase 0 - Discovery & Planning

---

## Executive Summary

JobNimbus is building its **first public API product**: a multi-provider financing platform that enables 100,000+ contractors to offer financing solutions from multiple providers (WiseTack, Sunlight Financial, FinTurf, and 40+ others) seamlessly integrated into the JobNimbus platform.

**Key Goals**:
1. Extract WiseTack/Sunlight integrations from SumoQuote codebase into standalone platform
2. Define standardized API contract that works for ANY financing provider
3. Build Stripe-quality developer experience (docs, SDKs, CLI tools)
4. Scale to 40+ providers with repeatable vetting/onboarding process
5. Show contractors and partners where financing data appears across JobNimbus platform

**North Star**: Match or exceed Stripe's developer experience quality

---

## Table of Contents

- [Phase 0: Foundation & Discovery](#phase-0-foundation--discovery-months-1-2)
  - [Current State Assessment](#1-current-state-assessment)
  - [SumoQuote Extraction Discovery](#1a-sumoquote-extraction-discovery-new)
  - [Platform Architecture Design](#2-platform-architecture-design)
  - [API Contract & Standards Definition](#2b-api-contract--standards-definition)
  - [Developer Experience Standards](#2c-developer-experience-dx-standards)
  - [Partner Vetting Framework](#2d-partner-vetting--onboarding-framework)
- [Phase 1: Core Platform MVP](#phase-1-core-platform-mvp-months-3-5)
- [Phase 2: Multi-Provider Support](#phase-2-multi-provider-support-months-6-8)
- [Phase 3: UI SDK & Partner Tools](#phase-3-ui-sdk--partner-tools-months-9-11)
- [Phase 4: Scale & Optimization](#phase-4-scale--optimization-months-12-15)
- [Phase 5: Advanced Features](#phase-5-advanced-features-months-16-18)

---

## Phase 0: Foundation & Discovery (Months 1-2)

### Goals

- Audit existing Sunlight Financial integration
- **Extract WiseTack/Sunlight from SumoQuote codebase** ⚠️ NEW
- Define platform architecture and API standards
- **Decide on UI SDK integration approach** ⚠️ NEW
- Establish partner onboarding requirements
- Create technical specifications

---

### 1. Current State Assessment

**What exists today:**

- ✅ **Sunlight Financial fully integrated**
  - Contact/Job financing application URLs
  - Settings management (`IntegrationSettings`)
  - Webhook status updates (`sunlightStatusAndId` on Jobs/Contacts)
  - Product selection and quote generation
  - HubSpot integration for tracking
- ✅ **Payment infrastructure via Payrix/GlobalPay**
- ✅ **Settings UI framework** (`payment-settings-frontend`)
- ⚠️ **Gap**: No public API, no partner SDK, single-provider hardcoded

---

### 1A. SumoQuote Extraction Discovery (NEW)

**⚠️ CRITICAL GAP DISCOVERED: SumoQuote Codebase**

**Current State**:
- **WiseTack integration currently being built in SumoQuote AWS codebase**
  - Location: `SumoQuote.Services/Integrations/Financing/Wisetack/`
  - Status: Step 1 MVP in progress (20% complete - 6/30 tasks)
  - Components: HTTP Client, Webhook Validator, Provider Registration
  - Data: Stored in SumoQuote DocumentDB (`WisetackIntegration`, `FinancingProject`)
- **Sunlight Financial has implementations in BOTH codebases**
  - SumoQuote: `SumoQuote.Services/Integrations/Financing/SunlightFinancial/`
  - dotnet-monolith: `App/Fintech/SunlightFinancial/`
  - Bridge code: `App/Sales/SumoQuote/` (DTO mapping, SSO)
- **SumoQuote is separate external service**
  - SSO integration via `SumoQuoteSingleSignOnService`
  - Separate database and AWS infrastructure
  - Used for estimate/quote generation (separate from core JobNimbus platform)

---

#### Critical Questions to Answer (Phase 0)

**1. Extraction Strategy Decision**

- [ ] **Option A**: Extract to new standalone "Financing Platform" microservice (recommended)
  - Pros: Clean separation, scales independently, easier partner SDK
  - Cons: New infrastructure, data migration complexity
- [ ] **Option B**: Migrate to dotnet-monolith `App/Fintech/Financing/`
  - Pros: Leverages existing infrastructure, simpler deployment
  - Cons: Monolith grows, harder to scale financing separately
- [ ] **Option C**: Keep in SumoQuote, add API layer on top
  - Pros: Minimal immediate work
  - Cons: Financing tied to estimate product, not platform-wide

**2. Data Migration Strategy**

- [ ] What data needs to migrate from SumoQuote DB?
  - `WisetackIntegration` documents (merchant IDs, settings)
  - `FinancingProject` documents (application state, provider data)
  - `SunlightFinancialIntegration` documents
- [ ] How to handle data during migration?
  - Dual-write period (write to both old and new)
  - Read-through caching strategy
  - Cutover timeline and rollback plan
- [ ] Where does data live long-term?
  - New `financing_applications` table in PostgreSQL?
  - Continue using DocumentDB/CouchBase?
  - Hybrid approach (metadata in SQL, provider data in NoSQL)?

**3. Code Reuse vs Rewrite**

- [ ] Can we reuse existing WiseTack code from SumoQuote?
  - `WisetackQueryService` (HTTP client)
  - `WisetackWebhookValidator` (signature validation)
  - `WisetackTransactionService`, `WisetackMerchantIntegrationService`
- [ ] What needs rewriting for platform-wide use?
  - Remove SumoQuote-specific dependencies
  - Generalize authentication (not tied to SumoUserAuth)
  - Update data models for multi-product support

**4. Timeline and Phasing**

- [ ] Should WiseTack Step 1 MVP complete in SumoQuote first?
  - Benefit: Prove functionality before extraction
  - Risk: Rework required for extraction
- [ ] Or pause WiseTack work and extract Sunlight first?
  - Benefit: Pattern established before adding WiseTack
  - Risk: Delays WiseTack launch, duplicate Sunlight work

**5. SumoQuote Relationship Post-Migration**

- [ ] Does SumoQuote still call financing APIs?
  - Yes: SumoQuote becomes consumer of new financing platform API
  - No: Financing fully separated from estimate product
- [ ] How does estimate→financing integration work?
  - Frontend SDK embedded in SumoQuote UI?
  - Deep link back to JobNimbus for financing?
  - Financing options shown in estimate view?

---

#### Recommended Discovery Tasks (Phase 0)

- [ ] **Map all SumoQuote→Financing touchpoints**
  - API calls, data dependencies, UI components
  - Document in architecture diagram
- [ ] **Prototype extraction for ONE provider (Sunlight)**
  - Build new `FinancingProvider` abstraction
  - Migrate one endpoint as proof-of-concept
  - Measure effort and identify blockers
- [ ] **Define cutover criteria**
  - What signals readiness for migration?
  - How to validate no data loss?
  - Rollback procedures

---

### 2. Platform Architecture Design

```
┌─────────────────────────────────────────────────────┐
│          JobNimbus Partner Platform                 │
├─────────────────────────────────────────────────────┤
│  Public API Layer                                   │
│  - Partner Authentication (OAuth 2.0)               │
│  - Rate Limiting & Quotas                          │
│  - API Gateway (API Management)                    │
├─────────────────────────────────────────────────────┤
│  Partner SDK (UI Components)                        │
│  - React Components Library                         │
│  - Financing Widget Framework                      │
│  - Event System & Callbacks                        │
├─────────────────────────────────────────────────────┤
│  Financing Abstraction Layer                        │
│  - Provider Registry                                │
│  - Standardized Financing Interface                │
│  - Multi-Provider Orchestration                    │
├─────────────────────────────────────────────────────┤
│  Product Integration Points                         │
│  - Settings (Provider Management)                   │
│  - Marketing (Lead Qualification)                   │
│  - Estimates (Financing Options)                    │
│  - Invoices (Payment Plans)                        │
│  - Jobs (Application Tracking)                     │
│  - Communications (Customer Notifications)          │
│  - Calendar (Appointment Integration)               │
└─────────────────────────────────────────────────────┘
```

---

### 2B. API Contract & Standards Definition ⚠️ CRITICAL - THE MISSING PIECE

**THE MISSING PIECE: JobNimbus Financing Partner API**

This is the **standardized API contract** that ALL financing providers must implement to integrate with JobNimbus, regardless of their internal differences.

---

#### Core Principle: Provider-Agnostic API Design

**Goal**: Abstract differences between WiseTack, Sunlight, FinTurf, and 40+ other providers into a unified API that:
- Exposes **common financing operations** all providers support
- Handles **provider-specific features** through extensible metadata
- Enables JobNimbus to **add new providers without code changes**

---

#### Provider Comparison Analysis

⚠️ **Note**: Only documenting providers with confirmed API documentation

| Feature | Sunlight | WiseTack | FinTurf | Stripe-Affirm | Commonality |
|---------|----------|----------|---------|---------------|-------------|
| **API Documentation** | ✅ Complete | ✅ Complete | ✅ Complete | ✅ Complete (Stripe docs) | ✅ All documented |
| **Merchant Onboarding** | Manual portal | API-driven signup | Pre-configured | Stripe account required | ⚠️ Split (API vs Manual) |
| **Customer Pre-Qualification** | Soft credit pull | Soft credit pull | Offer estimation | N/A (Affirm decides at checkout) | ⚠️ Mixed support |
| **Application Creation** | DocuSign URL | Transaction link | Application URL | Stripe Payment Intent | ✅ Common (URL/token-based) |
| **Quote Generation** | Product-based quotes (HIS/HIN/HII) | Payment calculator | Offer stack estimation | Affirm calculates at checkout | ✅ Common (monthly payment) |
| **Credit Processing** | Integrated credit bureau | Integrated credit | Partner handles | Affirm handles | ⚠️ Split (JN vs Partner) |
| **Status Updates** | Webhooks + API poll | Webhooks | API poll only | Webhooks (Stripe) | ✅ Webhooks preferred |
| **Document Signing** | DocuSign (Sunlight hosts) | Partner handles | Partner handles | N/A | ⚠️ Provider-specific |
| **Funding/Payout** | Multi-draw payments | Single payout | Partner handles | Stripe handles | ⚠️ Provider-specific |
| **Loan Stipulations** | Supported (TTL, VOI, etc.) | Not applicable | Not applicable | Not applicable | ⚠️ Sunlight-specific |

**Key Insight**: 60-70% common operations, 30-40% provider-specific → Design API with **core operations** + **extensible `providerMetadata`**

---

### 🚨 TBD: Additional Financing Providers

**Providers Mentioned But NO API Documentation Yet:**

| Provider | Status | Information Needed |
|----------|--------|-------------------|
| **Service Finance** | ❌ No API docs | - API documentation URL<br>- Authentication method<br>- Core endpoints (merchant signup, consumer application, quotes)<br>- Webhook events and signature validation<br>- Co-borrower support details<br>- Unique features vs other providers |
| **GreenSky** | ❓ Unknown | - Do we plan to integrate?<br>- API documentation if available |
| **Dividend Finance** | ❓ Unknown | - Do we plan to integrate?<br>- Solar-specific financing needs |
| **Other providers?** | ❓ Unknown | - What other providers are we considering?<br>- Industry-specific providers (HVAC, roofing, etc.) |

**Questions to Answer (Phase 0):**

1. ❓ **What is the complete list of financing providers we want to support?**
   - Initial launch (Phase 1-2)?
   - Roadmap for Phase 3-5?
   - Industry-specific vs general purpose?

2. ❓ **For each provider without docs, what's the path forward?**
   - Request API documentation from provider?
   - Partner relationship already established?
   - Integration feasibility timeline?

3. ❓ **Are there provider-specific features we haven't documented?**
   - Review all 4 documented providers for edge cases
   - Identify must-have vs nice-to-have features
   - Document provider limitations

---

### 2D. Partner Vetting & Onboarding Framework 🔍 SCALING TO 40+ PROVIDERS

**THE CHALLENGE**: You have 40+ potential financing providers, but only 3-4 with documented APIs. How do you evaluate, prioritize, and onboard partners at scale WITHOUT custom-building every integration?

**THE SOLUTION**: Standardized vetting process + self-service onboarding framework

---

#### Three-Phase Partner Qualification Process

**Phase 1: Initial Screening (30-Minute Discovery Call)**

**Document**: [partner-qualification-checklist.md](./partner-qualification-checklist.md)

- Send screening questionnaire to all 40 providers
- Schedule 30-min call with interested providers
- Evaluate against must-have criteria
- Output: ✅ Green Light / ⚠️ Yellow Light / ❌ Red Light

**Phase 2: Technical Documentation Review (2-Week Turnaround)**

**Document**: [technical-integration-rfi.md](./technical-integration-rfi.md) *(to be created)*

- Send formal Request for Information (RFI) to qualified providers
- Request complete API documentation, sandbox access, webhook specs
- Review technical maturity using evaluation scorecard
- Output: Provider scored 1-5 on technical readiness

**Phase 3: Technical Deep Dive (2-Hour Evaluation)**

**Document**: [technical-evaluation-scorecard.md](./technical-evaluation-scorecard.md) *(to be created)*

- Score provider's API quality and developer experience
- Weighted rubric: API Quality (40%), Webhooks (25%), DX (20%), etc.
- Output: **Tier 1** (4.0-5.0) / **Tier 2** (3.0-3.9) / **Tier 3** (<3.0)

---

#### What YOU Send to THEM

**Document**: [partner-integration-guide.md](./partner-integration-guide.md) *(to be created)*

Give providers a clear technical spec:
- What we're building (.NET 8, React 18, AWS)
- Integration model (JobNimbus builds adapter, they provide API docs)
- Technical requirements (REST API, JSON, webhooks)
- Timeline (3-4 months from docs to production)
- What they get (100K+ contractors, co-marketing, no dev work)

---

#### Handling 40+ Providers Without API Docs

**Track 1: Proactive Outreach**
- Send RFI to all 40 providers
- Expected: 40% respond in 2 weeks, 30% after follow-up, 30% ghost

**Track 2: Manual Research**
- Google their developer docs
- Sign up for trial merchant account
- Check competitor integrations
- Call sales/support

**Track 3: Non-API Integration Methods (Fallback)**
- Manual setup (contractor enters merchant ID)
- CSV batch upload/download
- Email-based automation

---

### 2C. Developer Experience (DX) Standards 🎯 STRIPE-QUALITY BENCHMARK

**Goal**: Match or exceed Stripe's developer experience quality.

---

#### What Makes Stripe's DX Best-in-Class?

1. **Documentation Quality** - Clear, executable examples, interactive API reference
2. **Developer Tools** - CLI, webhook testing locally, API logs dashboard
3. **Onboarding Velocity** - "Hello World" in 5 minutes
4. **Error Messages** - Human-readable, actionable next steps
5. **SDKs & Libraries** - Official libraries in 8+ languages, idiomatic code

---

#### Documentation Standards (Stripe-Inspired)

**Every API endpoint MUST have:**
- Executable code examples (cURL, Node.js, Python, React)
- Request/response schemas with all fields documented
- Error codes with solutions
- Testing instructions with specific test cases
- Links to related endpoints

**Example Structure:**

```markdown
## Create Financing Application

`POST /api/v1/financing/applications`

### Request
```json
{
  "merchantId": "jn_merchant_abc123",
  "consumer": { ... },
  "loanDetails": { "requestedAmount": 25000.00 }
}
```

### Response
```json
{
  "id": "jn_app_def456",
  "status": "initiated",
  "applicationUrl": "https://provider.com/apply/token123"
}
```

### Example Code

**Node.js**
```javascript
const application = await client.applications.create({
  merchantId: 'jn_merchant_abc123',
  ...
});
```

### Errors
| Code | Description | Solution |
|------|-------------|----------|
| `merchant_not_found` | Merchant ID invalid | Verify merchant is registered |

### Testing
Use test merchant ID `jn_merchant_test_123`:
- `$1000` → Instant approval
- `$10000` → Declined
```

---

#### Error Message Standards (Stripe-Quality)

**✅ GOOD Error Message:**

```json
{
  "error": {
    "type": "validation_error",
    "code": "merchant_not_found",
    "message": "No merchant found with ID 'jn_merchant_abc123'",
    "param": "merchantId",
    "suggestion": "Verify the merchant is registered at https://app.jobnimbus.com/settings/financing",
    "docsUrl": "https://docs.jobnimbus.com/financing/errors#merchant-not-found",
    "requestId": "req_XYZ789"
  }
}
```

**Required fields:**
- `type`: Error category
- `code`: Machine-readable error code
- `message`: Human-readable description
- `suggestion`: Actionable next step
- `docsUrl`: Link to relevant docs
- `requestId`: For debugging

---

#### Developer Tools & Testing

**🛠️ JobNimbus CLI Tool (Like `stripe` CLI)**

```bash
# Install
npm install -g @jobnimbus/cli

# Login
jn login

# Start webhook listener
jn listen --forward-to http://localhost:3000/webhooks

# Test API calls
jn financing applications create \
  --merchant-id jn_merchant_abc123 \
  --amount 25000

# View API logs
jn logs --tail

# Generate mock webhook events
jn trigger financing.application.approved
```

**🧪 Test Mode vs Live Mode**

```javascript
// Test Mode (keys start with sk_test_)
const client = new FinancingClient('sk_test_ABC123');
// - Fake credit checks (always approve test amounts)
// - Instant webhooks
// - No real money

// Live Mode (keys start with sk_live_)
const client = new FinancingClient('sk_live_XYZ789');
// - Real provider integrations
// - Actual credit checks
```

---

#### SDK/Library Standards

**🔹 Node.js SDK Example**

```typescript
import FinancingClient from '@jobnimbus/financing-sdk';

const client = new FinancingClient('sk_test_YOUR_KEY', {
  apiVersion: '2025-01-15',
  timeout: 30000,
  maxRetries: 3,
});

// Resource-based API
const application = await client.applications.create({
  merchantId: 'jn_merchant_abc123',
  consumer: { ... },
  loanDetails: { requestedAmount: 25000 },
});

// Idempotency support
const application = await client.applications.create(
  { ... },
  { idempotencyKey: 'unique-id-123' }
);

// Webhook validation
const event = client.webhooks.constructEvent(
  req.body,
  req.headers['jn-signature'],
  process.env.WEBHOOK_SECRET
);
```

**SDK Requirements (ALL SDKs Must Have):**
- Auto-retry on network failures
- Idempotency support
- Webhook validation helpers
- Type safety (TypeScript, type hints)
- Auto-pagination for list endpoints

---

#### Quickstart Experience (5-Minute Integration)

**Goal**: Get from zero to first financing application in 5 minutes

1. Get API key (30 seconds)
2. Install SDK (30 seconds)
3. Create application (4 minutes)
4. Test webhooks (optional, 2 minutes)

---

### 3. Technical Specifications

- **API Standards Document**: RESTful conventions, versioning, error handling
- **Data Models**: Standardized financing entities (Application, Quote, Product, Status)
- **Security Requirements**: Partner authentication, data encryption, PII handling
- **SLA Definitions**: Uptime, latency, webhook delivery guarantees

---

## Phase 1: Core Platform MVP (Months 3-5)

### Goals

- Build financing API for single provider (Sunlight as reference)
- Implement provider abstraction layer
- Create merchant and consumer application flows
- Build webhook processing system

### Key Deliverables

#### 1. **Financing API Endpoints**

```
POST /api/v1/financing/merchant-applications
GET  /api/v1/financing/merchants/{merchantId}
POST /api/v1/financing/applications
GET  /api/v1/financing/applications/{id}
GET  /api/v1/financing/applications/{id}/status
POST /api/v1/financing/quotes/calculate
POST /api/v1/financing/webhooks/[provider]
```

#### 2. **Provider Adapter Pattern**

```csharp
public interface IFinancingProvider
{
    Task<MerchantApplication> CreateMerchantApplication(CreateMerchantRequest request);
    Task<ConsumerFinancingApplication> CreateApplication(CreateApplicationRequest request);
    Task<List<FinancingQuote>> GetQuotes(GetQuotesRequest request);
    Task<ApplicationStatus> GetApplicationStatus(string applicationId);
    Task<bool> ValidateWebhook(WebhookPayload payload, string signature);
}
```

#### 3. **Data Models**

- `MerchantApplication` - Contractor applies to offer financing
- `FinancingMerchant` - Approved merchant account
- `ConsumerFinancingApplication` - Homeowner loan application
- `FinancingQuote` - Loan product/payment option
- `ApplicationStatus` - Current state and timeline

#### 4. **Webhook System**

- Signature validation
- Event routing
- Retry logic
- Status update processing

---

## Phase 2: Multi-Provider Support (Months 6-8)

### Goals

- Add WiseTack and FinTurf providers
- Build provider registry and routing
- Implement multi-provider UI in Settings
- Create provider comparison tools

### Key Deliverables

#### 1. **Provider Registry**

- Dynamic provider discovery
- Capability-based routing
- Provider health monitoring
- Fallback provider logic

#### 2. **Settings UI Updates**

- Provider marketplace view (Finance Partner Cards from Figma)
- Multi-provider configuration
- Provider status dashboard
- Merchant account management per provider

#### 3. **Provider Comparison**

- Side-by-side quote comparison
- Approval odds estimation
- Fee transparency

---

## Phase 3: UI SDK & Partner Tools (Months 9-11)

### Goals

- Build React component library for financing
- Create developer documentation portal
- Launch CLI tools
- Onboard first external partners

### Key Deliverables

#### 1. **React SDK**

```tsx
import { FinancingProvider, useFinancingApplication } from '@jobnimbus/financing-react';

function App() {
  return (
    <FinancingProvider apiKey="pk_test_...">
      <EstimateView />
    </FinancingProvider>
  );
}

function EstimateView() {
  const { create, isLoading } = useFinancingApplication();
  
  const handleApply = async () => {
    const app = await create({
      merchantId: 'jn_merchant_abc123',
      consumer: customerData,
      loanDetails: { requestedAmount: 25000 },
    });
    
    window.location.href = app.applicationUrl;
  };

  return <button onClick={handleApply}>Apply for Financing</button>;
}
```

#### 2. **Documentation Portal**

- docs.jobnimbus.com/financing/
- Interactive API explorer
- Code examples in multiple languages
- Provider-specific guides

#### 3. **CLI Tool**

- Local webhook testing
- API call testing
- Mock event generation
- Request validation

---

## Phase 4: Scale & Optimization (Months 12-15)

### Goals

- Onboard 10+ additional providers
- Optimize performance and reliability
- Add advanced analytics
- Implement A/B testing framework

### Key Deliverables

#### 1. **Performance Optimization**

- Response time <500ms (p95)
- Webhook delivery <10 seconds
- 99.9% uptime SLA

#### 2. **Analytics Dashboard**

- Application funnel metrics
- Provider performance comparison
- Approval rate tracking
- Revenue attribution

#### 3. **A/B Testing**

- Provider recommendation engine
- Quote display optimization
- Application flow variations

---

## Phase 5: Advanced Features (Months 16-18)

### Goals

- Mobile SDK support (iOS/Android)
- Advanced provider routing
- AI-powered recommendations
- International expansion

### Key Deliverables

#### 1. **Mobile SDKs**

- Swift Package for iOS
- Android Module
- React Native support

#### 2. **Smart Routing**

- ML-based provider recommendations
- Customer profile matching
- Approval probability prediction

#### 3. **International Support**

- Multi-currency support
- Localization
- Regional compliance

---

## Success Metrics

### Phase 0 (Discovery)

- [ ] 40+ providers contacted
- [ ] 10+ providers qualified
- [ ] 4-6 Tier 1 partners identified
- [ ] All vetting documents created

### Phase 1 (MVP)

- [ ] 1 provider fully integrated (Sunlight)
- [ ] <2 second API response time (p95)
- [ ] 99% webhook delivery success

### Phase 2 (Multi-Provider)

- [ ] 3+ providers live
- [ ] 100+ active merchants
- [ ] 500+ consumer applications

### Phase 3 (SDK)

- [ ] Documentation site live
- [ ] CLI tool released
- [ ] 2+ external partners onboarded

### Long-Term

- [ ] 10+ providers integrated
- [ ] 1,000+ active merchants
- [ ] 10,000+ applications/month
- [ ] <5 minute developer onboarding time

---

## Risk Mitigation

### Technical Risks

1. **Provider API downtime**
   - Mitigation: Fallback providers, cached data, circuit breakers

2. **Data migration from SumoQuote**
   - Mitigation: Dual-write period, extensive testing, rollback plan

3. **Webhook delivery failures**
   - Mitigation: Retry logic, dead letter queue, manual reconciliation

### Business Risks

1. **Provider relationship management**
   - Mitigation: Clear partnership terms, regular check-ins, shared success metrics

2. **Compliance and regulatory**
   - Mitigation: Legal review, state-by-state analysis, provider certifications

3. **Customer adoption**
   - Mitigation: Phased rollout, extensive documentation, support resources

---

## Open Questions

### Phase 0 Questions (MUST ANSWER)

1. ❓ **SumoQuote Extraction**: Option A, B, or C? (Microservice, Monolith, or Keep in SumoQuote)
2. ❓ **UI SDK Approach**: Plugin, Extension, iframe, or Hybrid model?
3. ❓ **Data Storage**: PostgreSQL, MongoDB, or Hybrid?
4. ❓ **Complete Provider List**: What 40+ providers are we targeting?
5. ❓ **Launch Timeline**: When do we want to ship Phase 1 MVP?

### Discovery Items (TBD)

- [ ] Service Finance API documentation
- [ ] Provider fee structures and business terms
- [ ] State-by-state regulatory requirements
- [ ] PII handling and data residency requirements
- [ ] Co-marketing opportunities with providers

---

## Resources

### Documentation

- [Partner Qualification Checklist](./partner-qualification-checklist.md) ✅ CREATED
- [Technical Integration RFI](./technical-integration-rfi.md) ✅ CREATED
- [Technical Evaluation Scorecard](./technical-evaluation-scorecard.md) ✅ CREATED
- [Partner Integration Guide](./partner-integration-guide.md) ✅ CREATED

### API Documentation (Existing Providers)

- [Sunlight Financial API Requirements](./Sunlight%20Financial%20API%20Requirements.md)
- [Financing API Requirements](./Financing%20API%20Requirements.md) (WiseTack, FinTurf, Stripe-Affirm)

### Existing Code References

- SumoQuote WiseTack: `SumoQuote.Services/Integrations/Financing/Wisetack/`
- Sunlight (SumoQuote): `SumoQuote.Services/Integrations/Financing/SunlightFinancial/`
- Sunlight (Monolith): `App/Fintech/SunlightFinancial/`
- Bridge Code: `App/Sales/SumoQuote/`

### Design References

- [Figma: Fintech Marketplace](https://www.figma.com/design/27TLV3TQBqDgktbKP9FrW3/Fintech-Marketplace?node-id=93-47641)
- Finance Partner Card (332px × 222px)
- Status States: Pending, Installed, Uninstalled

### Inspiration

- [Stripe API Documentation](https://stripe.com/docs/api)
- [Stripe Developer Experience](https://stripe.com/docs/development)
- [Stripe CLI](https://stripe.com/docs/stripe-cli)

---

**Document Version**: 1.0  
**Last Updated**: 2025-01-23  
**Next Review**: After Phase 0 completion

