# JobNimbus Financing Platform
## Partner Integration Guide

**Version**: 1.0  
**Last Updated**: 2025-01-23  
**Audience**: Financing providers interested in integrating with JobNimbus

---

## Overview

JobNimbus is building a **multi-provider financing platform** to serve our **100,000+ contractors** nationwide. We're seeking financing partners who can integrate via our **Provider Adapter API** pattern to offer seamless financing solutions to homeowners and businesses.

**What We're Building**: A Stripe-quality developer platform where contractors can offer financing from multiple providers through a unified JobNimbus interface.

**Your Opportunity**: Access to 100,000+ active contractors processing billions in home improvement projects annually.

---

## Table of Contents

1. [Platform Overview](#platform-overview)
2. [Integration Model](#integration-model)
3. [Technical Requirements](#technical-requirements)
4. [Data Exchange Formats](#data-exchange-formats)
5. [Integration Process](#integration-process)
6. [What JobNimbus Provides](#what-jobnimbus-provides)
7. [Security & Compliance](#security--compliance)
8. [Support & Resources](#support--resources)
9. [Next Steps](#next-steps)

---

## Platform Overview

### What We're Building

**Platform Architecture**:
- **Backend**: .NET 8 (C#), hosted on AWS (us-east-1)
- **Frontend**: React 18 (TypeScript), Material-UI components
- **Database**: PostgreSQL (structured data), MongoDB (unstructured provider data)
- **API Style**: RESTful, JSON, versioned (v1, v2, etc.)
- **Authentication**: API keys (Phase 1), OAuth 2.0 (Phase 2)

**Integration Model**:  
JobNimbus builds an **adapter layer** for each provider. You provide API documentation and support; we handle the integration.

```
┌─────────────────────────────────────────────────────┐
│          JobNimbus Financing Platform               │
├─────────────────────────────────────────────────────┤
│  Public API Layer (OAuth 2.0, Rate Limiting)        │
├─────────────────────────────────────────────────────┤
│  Provider Adapters                                  │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐     │
│  │  Sunlight  │ │  WiseTack  │ │   Your     │     │
│  │  Adapter   │ │  Adapter   │ │  Adapter   │     │
│  └────────────┘ └────────────┘ └────────────┘     │
├─────────────────────────────────────────────────────┤
│  YOUR API                                           │
└─────────────────────────────────────────────────────┘
```

**Key Point**: You do NOT need to build anything on your end. JobNimbus builds the adapter that calls your existing API.

---

### Product Integration Points

Your financing solutions will be available across the JobNimbus platform:

| Product Area | Integration | User Experience |
|--------------|-------------|-----------------|
| **Settings** | Provider configuration, merchant account management | Contractors configure provider settings |
| **Marketing** | Lead qualification, pre-qualification | Contractors pre-qualify leads before estimate |
| **Estimates/Sales** | Financing options display, "as low as" monthly payment | Contractors show financing options in estimates |
| **Invoices** | Payment plans, financing application | Contractors offer financing at checkout |
| **Jobs** | Application tracking, status updates | Contractors track financing status per job |
| **Communications** | Automated notifications, status alerts | Homeowners receive application updates |
| **Calendar** | Appointment scheduling tied to funding | Schedule work based on financing approval |

---

### Target Customers

**Contractors (Merchants)**:
- 100,000+ active contractors
- Industries: Roofing, HVAC, Solar, Kitchen/Bath remodeling, General contracting
- Average project value: $15,000 - $50,000
- Located nationwide (all 50 states)

**Homeowners (Consumers)**:
- End consumers financing home improvement projects
- Typical loan amounts: $5,000 - $100,000
- Credit profiles: Prime to near-prime
- Property owners and renters

---

## Integration Model

### How It Works

**Your Responsibilities**:
1. ✅ Provide complete API documentation
2. ✅ Provide sandbox/test environment and credentials
3. ✅ Respond to technical questions during integration
4. ✅ Support webhook testing and validation
5. ✅ Participate in staging pilot testing

**JobNimbus Responsibilities**:
1. ✅ Build provider adapter (implements our `IFinancingProvider` interface)
2. ✅ Build data transformation logic (your API ↔ our data models)
3. ✅ Build UI components for your products (Settings, Estimates, etc.)
4. ✅ Handle webhook processing and status updates
5. ✅ Provide ongoing maintenance and support

**Timeline**: 3-4 months from documentation to production launch

---

### Integration Architecture

**JobNimbus Provider Adapter Pattern**:

```csharp
public interface IFinancingProvider
{
    // Merchant/Contractor Operations
    Task<MerchantApplication> CreateMerchantApplication(CreateMerchantRequest request);
    Task<FinancingMerchant> GetMerchant(string merchantId);
    
    // Consumer Application Operations
    Task<ConsumerFinancingApplication> CreateApplication(CreateApplicationRequest request);
    Task<ApplicationStatus> GetApplicationStatus(string applicationId);
    
    // Quote/Product Operations
    Task<List<FinancingQuote>> GetQuotes(GetQuotesRequest request);
    
    // Webhook Operations
    Task<bool> ValidateWebhook(WebhookPayload payload, string signature);
    Task ProcessWebhookEvent(WebhookEvent event);
}
```

We build this adapter to translate between JobNimbus's standardized interface and your specific API.

---

## Technical Requirements

### Must-Have Requirements

Your API **MUST** support these core operations:

#### 1. Merchant/Contractor Onboarding

| Requirement | Description | Implementation |
|-------------|-------------|----------------|
| **Merchant Signup** | Contractors can register as merchants with your service | API endpoint OR manual portal |
| **Merchant Status** | Check if merchant is approved/active | API endpoint (GET) |
| **Merchant Settings** | Retrieve merchant configuration (min/max loan amounts, fees) | API endpoint (GET) |

#### 2. Consumer Financing Applications

| Requirement | Description | Implementation |
|-------------|-------------|----------------|
| **Create Application** | Generate financing application for homeowner | API endpoint (POST) |
| **Application URL** | Return URL where consumer completes application | Response field |
| **Check Status** | Query current application status | API endpoint (GET) |
| **Status Updates** | Notify JobNimbus of status changes | Webhooks (preferred) OR Polling API |

#### 3. Loan Products & Quotes

| Requirement | Description | Implementation |
|-------------|-------------|----------------|
| **List Products** | Retrieve available loan products | API endpoint (GET) OR static list |
| **Calculate Quote** | Calculate monthly payment for loan amount | API endpoint (POST) |
| **Product Details** | Return APR, term, fees, requirements | API response |

---

### API Standards

#### REST API

- ✅ **Must Use**: JSON for request/response
- ✅ **Must Use**: Standard HTTP methods (GET, POST, PUT, DELETE)
- ✅ **Must Use**: Appropriate HTTP status codes
- ✅ **Must Use**: HTTPS (TLS 1.2+)
- ❌ **Cannot Use**: SOAP, XML, or non-REST protocols

#### Authentication

We support the following authentication methods:

- ✅ **API Keys** (Bearer token in `Authorization` header)
- ✅ **HTTP Basic Authentication**
- ✅ **OAuth 2.0** (client credentials flow)
- ❌ **Not Supported**: Session-based auth, cookies

**Example**:
```http
GET /api/v1/merchants/12345
Authorization: Bearer your_api_key_here
Content-Type: application/json
```

#### Webhooks (Strongly Preferred)

**Why Webhooks?**  
Real-time updates provide better UX than polling. Contractors see instant status updates without refreshing.

**Requirements**:
- JobNimbus registers webhook URL with your API
- You POST event data to our endpoint when status changes
- You include signature in header for validation (HMAC SHA-256 preferred)
- You retry failed deliveries (recommended: 3 retries, exponential backoff)

**Minimum Required Events**:
- `application.status_changed` - Application moves to new status
- `application.funded` - Loan funded, contractor paid

**Optional Events**:
- `application.documents_ready` - Documents available for signing
- `merchant.status_changed` - Merchant account status updated

---

### Performance Requirements

We expect the following from your API:

| Metric | Target | Acceptable | Notes |
|--------|--------|------------|-------|
| **Uptime** | 99.9% | 99.5% | Max 4 hours downtime/month |
| **Response Time (p95)** | <1 second | <2 seconds | For application creation |
| **Response Time (p99)** | <2 seconds | <5 seconds | For status checks |
| **Webhook Delivery** | <10 seconds | <30 seconds | From event occurrence |
| **Rate Limits** | 100 req/min | 50 req/min | Per merchant minimum |

**We Provide**:
- Exponential backoff on retries
- Circuit breaker pattern (stop calling if you're down)
- Request queuing during outages

---

## Data Exchange Formats

### Standard Data Formats

All data exchange uses these standards:

| Data Type | Format | Example |
|-----------|--------|---------|
| **Dates** | ISO 8601 | `2025-01-23T14:30:00Z` |
| **Currency** | USD, decimal | `25000.00` (not `$25,000` or `25000`) |
| **Phone Numbers** | E.164 format | `+15551234567` (not `(555) 123-4567`) |
| **SSN** | 9 digits | `123456789` (no dashes) |
| **Zip Codes** | 5 or 9 digits | `80202` or `80202-1234` |
| **States** | 2-letter code | `CO` (not `Colorado`) |

---

### Example API Calls

#### Create Financing Application

**Request**:
```http
POST /api/v1/applications HTTP/1.1
Authorization: Bearer sk_test_your_key_here
Content-Type: application/json

{
  "merchantId": "your_merchant_id_12345",
  "consumer": {
    "firstName": "Jane",
    "lastName": "Doe",
    "email": "jane@example.com",
    "phone": "+15551234567",
    "address": {
      "street": "123 Main St",
      "city": "Denver",
      "state": "CO",
      "zipCode": "80202"
    }
  },
  "loan": {
    "requestedAmount": 25000.00,
    "purpose": "Roof replacement"
  },
  "project": {
    "type": "roofing",
    "propertyAddress": {
      // Same as consumer address or different
    }
  },
  "callbackUrl": "https://api.jobnimbus.com/webhooks/financing/your-provider",
  "externalId": "jn_job_abc123" // JobNimbus job ID for tracking
}
```

**Response**:
```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "applicationId": "your_app_id_xyz789",
  "status": "initiated",
  "applicationUrl": "https://your-platform.com/apply/secure-token-123",
  "expiresAt": "2025-01-28T14:30:00Z",
  "createdAt": "2025-01-23T14:30:00Z"
}
```

#### Get Application Status

**Request**:
```http
GET /api/v1/applications/your_app_id_xyz789 HTTP/1.1
Authorization: Bearer sk_test_your_key_here
```

**Response**:
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "applicationId": "your_app_id_xyz789",
  "status": "approved",
  "statusDetails": {
    "subStatus": "pending_documents",
    "statusDate": "2025-01-23T15:00:00Z"
  },
  "loanDetails": {
    "approvedAmount": 22000.00,
    "interestRate": 4.99,
    "termMonths": 120,
    "monthlyPayment": 231.25
  },
  "documentsUrl": "https://your-platform.com/sign/token-456",
  "nextSteps": [
    "Sign loan documents",
    "Verify bank account"
  ]
}
```

#### Webhook Event

**Your System → JobNimbus**:
```http
POST /api/v1/financing/webhooks/your-provider HTTP/1.1
Content-Type: application/json
X-Your-Signature: sha256=abc123def456...

{
  "eventId": "evt_unique_id_123",
  "eventType": "application.approved",
  "timestamp": "2025-01-23T15:00:00Z",
  "data": {
    "applicationId": "your_app_id_xyz789",
    "externalId": "jn_job_abc123",
    "status": "approved",
    "approvedAmount": 22000.00,
    "interestRate": 4.99,
    "termMonths": 120,
    "monthlyPayment": 231.25
  }
}
```

**JobNimbus Response**:
```http
HTTP/1.1 200 OK

{ "received": true }
```

---

### Error Response Format

**Standard Error Response**:
```json
{
  "error": {
    "code": "invalid_amount",
    "message": "Loan amount must be between $5,000 and $100,000",
    "param": "loan.requestedAmount",
    "value": "150000",
    "docsUrl": "https://your-docs.com/errors#invalid-amount"
  }
}
```

**Required HTTP Status Codes**:
- `200` - Success
- `400` - Bad request (invalid data)
- `401` - Unauthorized (invalid API key)
- `404` - Not found (merchant/application doesn't exist)
- `422` - Unprocessable (declined, ineligible, etc.)
- `429` - Rate limit exceeded
- `500` - Server error
- `503` - Service unavailable

---

## Integration Process

### 5-Phase Integration Timeline

#### Phase 1: Technical Discovery (2 weeks)

**Activities**:
- [ ] You provide API documentation (Swagger/OpenAPI or PDF)
- [ ] JobNimbus technical team reviews documentation
- [ ] Joint technical call to clarify questions (2 hours)
- [ ] You provide sandbox credentials
- [ ] JobNimbus tests your sandbox API

**Deliverables**:
- ✅ Complete API documentation reviewed
- ✅ Sandbox access confirmed
- ✅ Integration plan agreed upon

---

#### Phase 2: Development (4-6 weeks)

**Activities**:
- [ ] JobNimbus builds provider adapter (`IFinancingProvider` implementation)
- [ ] JobNimbus builds data transformation layer
- [ ] JobNimbus builds UI components (Settings, Estimates, etc.)
- [ ] Weekly check-in calls to address questions
- [ ] You provide support for API questions

**Deliverables**:
- ✅ Provider adapter complete
- ✅ Unit tests passing (80%+ coverage)
- ✅ Can create merchants, applications, and process webhooks

---

#### Phase 3: Testing (2 weeks)

**Activities**:
- [ ] End-to-end integration testing in sandbox
- [ ] Webhook delivery and signature validation testing
- [ ] Error scenario testing (declines, timeouts, etc.)
- [ ] Performance and load testing
- [ ] Security review and vulnerability testing

**Test Scenarios**:
- Create 5-10 test merchants
- Process 50-100 test applications
- Test all status transitions
- Validate all webhook events

**Deliverables**:
- ✅ All test scenarios passing
- ✅ Webhook delivery 99%+ success rate
- ✅ Performance benchmarks met
- ✅ Security review passed

---

#### Phase 4: Staging Pilot (2-4 weeks)

**Activities**:
- [ ] Deploy to JobNimbus staging environment
- [ ] Onboard 5-10 real contractors (beta users)
- [ ] Process 20-50 real applications
- [ ] Monitor closely for issues
- [ ] Fix any bugs discovered
- [ ] Gather contractor and homeowner feedback

**Success Criteria**:
- 90%+ application success rate
- <5 minutes average time to create application
- Zero critical bugs
- Positive feedback from beta users

**Deliverables**:
- ✅ Staging pilot complete
- ✅ All issues resolved
- ✅ Go-live approval obtained

---

#### Phase 5: Production Launch (1 week)

**Activities**:
- [ ] Deploy to JobNimbus production environment
- [ ] Switch to production API credentials
- [ ] Enable for limited user group (10-20 contractors)
- [ ] Monitor for 48 hours with no issues
- [ ] Gradually roll out to broader audience
- [ ] Announce partnership publicly

**Rollout Strategy**:
- Week 1: 10-20 beta contractors
- Week 2: 100 contractors (if no issues)
- Week 3: 500 contractors
- Week 4: General availability (100,000+ contractors)

**Deliverables**:
- ✅ Production launch complete
- ✅ Monitoring dashboards active
- ✅ Support processes in place
- ✅ Co-marketing materials ready

---

### Integration Timeline Summary

| Phase | Duration | Cumulative |
|-------|----------|------------|
| Phase 1: Discovery | 2 weeks | 2 weeks |
| Phase 2: Development | 4-6 weeks | 6-8 weeks |
| Phase 3: Testing | 2 weeks | 8-10 weeks |
| Phase 4: Staging Pilot | 2-4 weeks | 10-14 weeks |
| Phase 5: Production | 1 week | 11-15 weeks |

**Total: 3-4 months from kickoff to production**

---

## What JobNimbus Provides

### No Development Required From You

✅ JobNimbus builds the entire integration  
✅ JobNimbus maintains the adapter code  
✅ JobNimbus handles UI/UX for all products  
✅ JobNimbus processes webhooks and status updates  
✅ JobNimbus provides contractor support  

**You Only Need To**:
- Provide API documentation
- Provide sandbox/test credentials
- Answer technical questions
- Support production issues (your API)

---

### Partner Benefits

#### 1. Access to 100,000+ Active Contractors

- **Industries**: Roofing, HVAC, Solar, Kitchen/Bath, General Contracting
- **Geographic Coverage**: All 50 US states
- **Project Volume**: Billions in annual project value
- **Growth**: Growing 20%+ year-over-year

#### 2. Dedicated Integration Engineer

- Single point of contact throughout integration
- Weekly check-in calls during development
- Priority support for technical issues
- Post-launch ongoing relationship

#### 3. Settings UI for Merchant Management

We build a beautiful Settings UI where contractors can:
- Connect their financing provider accounts
- View account status and capabilities
- Configure financing preferences
- Manage merchant credentials

**Design Reference**: Finance Partner Card (332px × 222px) from Figma

#### 4. Product Integration Across Platform

Your financing options appear in:
- Estimate builder (alongside pricing)
- Invoice checkout
- Job detail pages
- Customer communication templates
- Marketing materials

#### 5. Co-Marketing Opportunities

- Joint case studies and success stories
- Co-branded marketing materials
- Webinars for contractors
- Blog posts and press releases
- Social media promotion
- Inclusion in JobNimbus partner directory

#### 6. Analytics & Reporting

Access to:
- Application volume metrics
- Approval rate tracking
- Average loan amounts
- Contractor adoption rates
- Regional performance data

---

## Security & Compliance

### Our Security Standards

**Infrastructure**:
- AWS hosting with SOC 2 Type II certification
- Data encryption at rest (AES-256)
- Data encryption in transit (TLS 1.2+)
- Regular security audits and penetration testing

**API Security**:
- API key rotation supported
- OAuth 2.0 for production (Phase 2)
- IP whitelisting available
- Rate limiting per merchant
- Request/response logging (PII redacted)

**Compliance**:
- GDPR compliant
- CCPA compliant
- PCI DSS compliant (Stripe for payments)
- Regular compliance audits

---

### Your Security Requirements

**What We Need From You**:

#### 1. API Security
- [ ] TLS 1.2+ required for all API calls
- [ ] API key rotation supported
- [ ] No credentials in URLs or logs
- [ ] Rate limiting to prevent abuse

#### 2. Webhook Security
- [ ] Signature validation (HMAC SHA-256 or similar)
- [ ] Timestamp validation to prevent replay attacks
- [ ] HTTPS required for webhook endpoints

#### 3. PII Protection
- [ ] PII encrypted at rest
- [ ] PII encrypted in transit
- [ ] PII redacted in logs
- [ ] PII access controls in place

#### 4. Compliance
- [ ] GDPR compliant (if serving EU customers)
- [ ] CCPA compliant (if serving CA customers)
- [ ] State lending regulations followed
- [ ] Fair Credit Reporting Act (FCRA) compliant
- [ ] Equal Credit Opportunity Act (ECOA) compliant

#### 5. Certifications (Preferred)
- [ ] SOC 2 Type II
- [ ] PCI DSS (if handling credit cards)
- [ ] ISO 27001

---

### Data Handling

**What Data JobNimbus Stores**:
- Contractor/merchant account information
- Consumer/homeowner application data (name, email, phone, address)
- Loan application status and timeline
- Application identifiers and URLs
- Webhook event history

**What Data JobNimbus Does NOT Store**:
- Social Security Numbers (SSN)
- Date of Birth (DOB)
- Full credit reports
- Bank account numbers
- Payment card information

**PII Handling**:
- Consumer PII sent to your API during application creation
- PII not stored in JobNimbus database
- PII redacted in logs and monitoring
- PII access restricted to authorized personnel only

---

## Support & Resources

### Integration Support

**During Integration**:
- Dedicated integration engineer assigned to you
- Weekly check-in calls
- Email support: integrations@jobnimbus.com
- Slack channel (shared between teams)
- Response time: <4 hours for critical issues

**Post-Launch**:
- Ongoing technical support for API issues
- Quarterly business reviews
- Access to product roadmap
- Input on future platform features

---

### Documentation & Resources

**What We Provide**:
- This Integration Guide
- Technical RFI (Request for Information)
- Integration checklist and timeline
- Sample application flows
- Webhook testing tools

**What We Need From You**:
- Complete API documentation (Swagger/OpenAPI or PDF)
- Sandbox credentials and test instructions
- Webhook event catalog with payload examples
- Error code reference
- Support contact information

---

### Communication Channels

**Primary Contacts**:
- **Partnerships Team**: partnerships@jobnimbus.com
- **Technical Team**: integrations@jobnimbus.com
- **Support Escalation**: [emergency contact - to be provided]

**Regular Meetings**:
- Weekly check-ins during integration (30 minutes)
- Monthly check-ins post-launch (30 minutes)
- Quarterly business reviews (1 hour)

---

## Next Steps

### Interested in Partnering?

**Step 1: Initial Conversation** (30 minutes)

We'll discuss:
- Your financing products and target customers
- Technical capabilities and API availability
- Partnership goals and expectations
- Timeline and next steps

**Schedule a call**: [calendly link or email partnerships@jobnimbus.com]

---

**Step 2: Technical Documentation Request** (2 weeks)

We'll send you:
- Formal Technical Integration RFI
- Request for API documentation
- Request for sandbox access

You provide:
- Complete API documentation
- Sandbox credentials
- Responses to technical questions

---

**Step 3: Technical Evaluation** (1-2 weeks)

We'll:
- Review your API documentation
- Test your sandbox environment
- Score your technical maturity (Tier 1/2/3)
- Provide decision and timeline

You'll receive:
- Approval or conditional approval
- Proposed integration timeline
- Next steps for kickoff

---

**Step 4: Kickoff & Integration** (3-4 months)

- Commercial agreement signed
- Integration kickoff call
- 5-phase integration process (see above)
- Production launch

---

### Frequently Asked Questions

**Q: How long does integration take?**  
A: Typically 3-4 months from kickoff to production launch.

**Q: Do we need to build anything on our end?**  
A: No. JobNimbus builds the entire integration. You only need to provide API documentation and support.

**Q: What if we don't have webhooks?**  
A: Webhooks are strongly preferred for real-time updates, but we can work with polling if necessary.

**Q: What if our API changes?**  
A: We ask for 30 days notice for breaking changes. We'll update our adapter accordingly.

**Q: How much does it cost?**  
A: Integration is free. Commercial terms (revenue share, referral fees) are negotiated separately.

**Q: Can we be exclusive?**  
A: No. JobNimbus supports multiple financing providers to give contractors choice.

**Q: How many contractors will use our service?**  
A: Depends on contractor preference, but we have 100,000+ active contractors. Typical adoption: 5-10% of contractors actively use financing.

**Q: Can we co-market the partnership?**  
A: Absolutely! We love joint case studies, webinars, and blog posts.

**Q: What if we have issues in production?**  
A: We have 24/7 monitoring and on-call engineers. We'll work with you to resolve issues quickly.

---

## Contact Us

**Ready to get started?**

📧 Email: partnerships@jobnimbus.com  
🌐 Website: https://www.jobnimbus.com/partners  
📞 Phone: [to be provided]

**Follow us**:
- LinkedIn: https://www.linkedin.com/company/jobnimbus
- Twitter: https://twitter.com/jobnimbus

---

**Document Version**: 1.0  
**Last Updated**: 2025-01-23  
**Owner**: Financing Platform Team, JobNimbus

---

*JobNimbus is committed to building best-in-class integrations that benefit contractors, financing providers, and homeowners. We look forward to partnering with you!*

