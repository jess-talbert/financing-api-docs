# Technical Integration RFI
## Request for Information - JobNimbus Financing Partner API

**Purpose**: Formal request for technical documentation from financing providers  
**Audience**: Providers who passed Phase 1 screening  
**Timeline**: 2-week response requested

---

## Email Template

```
Subject: JobNimbus Financing Partnership - Technical Integration Requirements

Dear [Provider Name] Team,

Thank you for your interest in partnering with JobNimbus to provide financing solutions to our 100,000+ contractors. Based on our initial conversation, we're excited to move forward with a technical evaluation.

To assess integration feasibility, we need comprehensive API documentation and technical information. Please review the attached requirements and provide responses by [Date - 2 weeks from today].

We're targeting [Month] for our next integration wave and would love to include [Provider Name] if we can align on technical requirements.

Best regards,
[Your Name]
Product Manager, Financing Platform
JobNimbus
[email]
[phone]
```

---

## Section 1: API Documentation (REQUIRED)

Please provide complete API documentation including:

### 1.1 Base Information

- [ ] **Sandbox Environment**
  - Base URL: _________________
  - Test credentials available? Yes / No
  - If yes, please provide or indicate how to obtain

- [ ] **Production Environment**
  - Base URL: _________________
  - Credential provisioning process: _________________

- [ ] **API Documentation Format**
  - [ ] Swagger/OpenAPI 3.0 specification (preferred)
  - [ ] PDF documentation
  - [ ] Web-based developer portal
  - [ ] Other: _________________
  - URL or attachment: _________________

### 1.2 Authentication Method

- [ ] Authentication type:
  - [ ] API Keys (Bearer token)
  - [ ] HTTP Basic Authentication
  - [ ] OAuth 2.0 (specify flow: ________________)
  - [ ] Other: _________________

- [ ] Credential rotation:
  - Supported? Yes / No
  - Process: _________________

- [ ] Security requirements:
  - TLS version: _________________
  - IP whitelist required? Yes / No
  - Additional security: _________________

### 1.3 Core API Endpoints

Please provide endpoint documentation including request/response examples for:

#### Merchant Registration/Onboarding

- [ ] **Endpoint**: `[METHOD] [PATH]`
- [ ] **Purpose**: How contractors become approved merchants
- [ ] **Request schema**: (attach or describe)
- [ ] **Response schema**: (attach or describe)
- [ ] **Required fields**: _________________
- [ ] **Average response time**: _________________
- [ ] **Is this API-driven or manual portal?** _________________

#### Consumer Loan Application Creation

- [ ] **Endpoint**: `[METHOD] [PATH]`
- [ ] **Purpose**: Create financing application for homeowner
- [ ] **Request schema**: (attach or describe)
- [ ] **Response schema**: (attach or describe)
- [ ] **Required fields**: _________________
- [ ] **Returns application URL?** Yes / No
- [ ] **Can customer data be pre-filled?** Yes / No

#### Loan Quote/Product Retrieval

- [ ] **Endpoint**: `[METHOD] [PATH]`
- [ ] **Purpose**: Get loan products and payment calculations
- [ ] **Request schema**: (attach or describe)
- [ ] **Response schema**: (attach or describe)
- [ ] **Can calculate without creating application?** Yes / No
- [ ] **What inputs are required?** _________________

#### Application Status Checking

- [ ] **Endpoint**: `[METHOD] [PATH]`
- [ ] **Purpose**: Check current status of application
- [ ] **Request schema**: (attach or describe)
- [ ] **Response schema**: (attach or describe)
- [ ] **Polling frequency recommendation**: _________________
- [ ] **Rate limits**: _________________

#### Webhook Event Registration (if supported)

- [ ] **Endpoint**: `[METHOD] [PATH]`
- [ ] **Purpose**: Register webhook endpoint for status updates
- [ ] **Request schema**: (attach or describe)
- [ ] **Response schema**: (attach or describe)

### 1.4 Data Models

Please provide JSON schemas or detailed field descriptions for:

- [ ] **Merchant/Contractor object**
- [ ] **Consumer/Homeowner object**
- [ ] **Loan Application object**
- [ ] **Loan Product/Quote object**
- [ ] **Application Status object**
- [ ] **Any other core entities**

### 1.5 Error Handling

- [ ] **Error response format**: (provide example)
- [ ] **HTTP status codes used**: _________________
- [ ] **Error code taxonomy**: (provide list or link)
- [ ] **Retry recommendations**: _________________

### 1.6 Rate Limits

- [ ] **Requests per minute**: _________________
- [ ] **Requests per hour**: _________________
- [ ] **Requests per day**: _________________
- [ ] **Rate limit headers**: _________________
- [ ] **Burst allowance**: _________________
- [ ] **Rate limit exceeded response**: (provide example)

---

## Section 2: Webhook Integration (if supported)

### 2.1 Webhook Events

Please document ALL supported webhook events:

| Event Name | Description | When Fired | Payload Example |
|------------|-------------|------------|-----------------|
| ____________ | ____________ | ____________ | (attach) |
| ____________ | ____________ | ____________ | (attach) |

**Minimum Required Events:**
- Application status changed (initiated, submitted, approved, declined, etc.)
- Application funded/completed
- Merchant status changed

### 2.2 Webhook Delivery

- [ ] **Delivery method**: HTTP POST to our endpoint
- [ ] **Retry policy**: _________________
- [ ] **Retry attempts**: _________________
- [ ] **Retry interval**: _________________
- [ ] **Max retry window**: _________________
- [ ] **Timeout**: _________________

### 2.3 Webhook Security

- [ ] **Signature validation method**:
  - [ ] HMAC SHA-256
  - [ ] SHA-256 Hash
  - [ ] JWT Signature
  - [ ] Other: _________________

- [ ] **Signature location**: (header name or body field)
- [ ] **Signature calculation**: (provide example code if possible)
- [ ] **Timestamp validation**: Required? Yes / No
- [ ] **Replay prevention**: How implemented? _________________

### 2.4 Webhook Testing

- [ ] **Can webhooks be triggered manually in sandbox?** Yes / No
- [ ] **Test event generation**: Describe process _________________

---

## Section 3: Integration Patterns

### 3.1 Merchant Onboarding Flow

Please describe the complete merchant onboarding process:

1. **Initiation**: 
   - [ ] API call to create merchant application
   - [ ] Contractor redirected to portal
   - [ ] Other: _________________

2. **Required Information**:
   - [ ] Business name
   - [ ] Tax ID / EIN
   - [ ] Business type (LLC, Corp, etc.)
   - [ ] Years in business
   - [ ] Annual revenue
   - [ ] Bank account information
   - [ ] Owner information
   - [ ] Other: _________________

3. **Approval Process**:
   - Average approval time: _________________
   - Approval criteria: _________________
   - Rejection reasons: _________________
   - Appeal process: _________________

4. **Post-Approval**:
   - [ ] Merchant ID provided via API
   - [ ] Merchant ID provided via email
   - [ ] Additional setup required? _________________

### 3.2 Consumer Application Flow

Please describe the complete consumer financing application process:

1. **Application Creation**:
   - [ ] JobNimbus calls API to create application
   - [ ] Returns application URL
   - [ ] Can pre-fill: [ ] Name [ ] Email [ ] Phone [ ] Address [ ] Loan amount
   - [ ] Application expiration: _________________

2. **Consumer Completes Application**:
   - [ ] Via web browser (desktop/mobile)
   - [ ] Via SMS link
   - [ ] Via embedded iframe
   - [ ] Other: _________________

3. **Required Consumer Information**:
   - [ ] Personal: Name, DOB, SSN, contact info
   - [ ] Employment: Employer, income, employment length
   - [ ] Property: Address, ownership status
   - [ ] Co-borrower: Supported? Yes / No
   - [ ] Other: _________________

4. **Decision Timeline**:
   - Instant decision? Yes / No
   - If no, typical decision time: _________________
   - Pending reasons: _________________

5. **Post-Decision**:
   - [ ] Webhook sent to JobNimbus
   - [ ] Email to consumer
   - [ ] Email to contractor
   - [ ] Other notifications: _________________

### 3.3 Loan Products & Quotes

1. **Product Retrieval**:
   - [ ] Static product list (provide list)
   - [ ] Dynamic via API (endpoint: _________________)
   - [ ] Merchant-specific products

2. **Quote Calculation**:
   - **Inputs required**:
     - [ ] Loan amount
     - [ ] Credit score (estimated)
     - [ ] Income
     - [ ] Other: _________________
   
   - **Outputs provided**:
     - [ ] Monthly payment
     - [ ] Interest rate / APR
     - [ ] Term length (months)
     - [ ] Total interest
     - [ ] Total payback amount
     - [ ] Merchant fees
     - [ ] Other: _________________

3. **Promotional Products**:
   - [ ] Do you offer 0% APR periods?
   - [ ] Do you offer deferred interest?
   - [ ] Do you offer same-as-cash?
   - [ ] Other promotions: _________________

### 3.4 Status Management

Please map your status values to descriptions:

| Your Status Value | Meaning | Is this a terminal state? |
|-------------------|---------|---------------------------|
| _________________ | _________________ | Yes / No |
| _________________ | _________________ | Yes / No |
| _________________ | _________________ | Yes / No |

**Status Flow Diagram**: (attach or describe typical status progression)

**Edge Cases**:
- Application expires after: _________________
- Application can be cancelled: Yes / No
- Application can be refunded: Yes / No
- Multiple applications per project: Allowed? Yes / No

---

## Section 4: Unique Features

### 4.1 Pre-Qualification

- [ ] **Supported?** Yes / No
- [ ] **API endpoint**: _________________
- [ ] **Soft credit pull**: Yes / No
- [ ] **Returns approval odds**: Yes / No
- [ ] **Returns estimated terms**: Yes / No

### 4.2 Co-Borrowers

- [ ] **Supported?** Yes / No
- [ ] **Maximum co-borrowers**: _________________
- [ ] **Relationship types**: _________________
- [ ] **Joint credit check**: Yes / No

### 4.3 Multi-Draw / Milestone Payments

- [ ] **Supported?** Yes / No
- [ ] **Maximum draws**: _________________
- [ ] **Draw request process**: _________________
- [ ] **API endpoint**: _________________

### 4.4 Document Requirements

- [ ] **Document signing required?** Yes / No
- [ ] **Signing provider**: (DocuSign, Notarize, etc.) _________________
- [ ] **Documents hosted by**: You / Us / Third-party
- [ ] **Document URL provided**: Yes / No

### 4.5 Stipulations

- [ ] **Supported?** Yes / No
- [ ] **Common stipulation types**: _________________
- [ ] **Stipulation submission**: Via API / Via portal / Email
- [ ] **Document upload**: Supported? Yes / No

### 4.6 SMS/Email Invitations

- [ ] **SMS invite to consumer**: Supported? Yes / No
- [ ] **Email invite to consumer**: Supported? Yes / No
- [ ] **JobNimbus sends invite**: Preferred? Yes / No
- [ ] **You send invite**: Preferred? Yes / No

---

## Section 5: Technical Requirements

### 5.1 Supported Technologies

- [ ] **Do you provide SDKs?** Yes / No
- [ ] **If yes, languages/frameworks**:
  - [ ] Node.js / JavaScript
  - [ ] Python
  - [ ] .NET / C#
  - [ ] Ruby
  - [ ] PHP
  - [ ] Other: _________________

### 5.2 API Versioning

- [ ] **Current API version**: _________________
- [ ] **Versioning strategy**: (URL path / Header / Other)
- [ ] **How are breaking changes handled?** _________________
- [ ] **Deprecation timeline**: _________________
- [ ] **Version sunset policy**: _________________

### 5.3 Performance Benchmarks

Please provide typical performance metrics:

- [ ] **Application creation response time**: _________ ms
- [ ] **Status check response time**: _________ ms
- [ ] **Quote calculation response time**: _________ ms
- [ ] **Webhook delivery time**: _________ seconds after event
- [ ] **p50 response time**: _________ ms
- [ ] **p95 response time**: _________ ms
- [ ] **p99 response time**: _________ ms

### 5.4 Uptime & Reliability

- [ ] **Uptime SLA**: _________ % (e.g., 99.9%)
- [ ] **Status page**: _________________
- [ ] **Planned maintenance**: How communicated? _________________
- [ ] **Incident notification**: How communicated? _________________

### 5.5 Data Retention

- [ ] **How long do you store application data?** _________________
- [ ] **Can historical data be retrieved via API?** Yes / No
- [ ] **Data deletion policy**: _________________
- [ ] **GDPR right to be forgotten**: Supported? Yes / No

### 5.6 PII Handling

- [ ] **What PII do you store?** _________________
- [ ] **PII encryption at rest**: Yes / No
- [ ] **PII encryption in transit**: Yes / No (TLS version: _______)
- [ ] **PII in logs**: Redacted? Yes / No
- [ ] **PII access controls**: Describe _________________

---

## Section 6: Security & Compliance

### 6.1 Certifications

- [ ] **SOC 2 Type II**: Yes / No (if yes, provide report or summary)
- [ ] **PCI DSS**: Yes / No / N/A (Level: _______)
- [ ] **ISO 27001**: Yes / No
- [ ] **Other certifications**: _________________

### 6.2 Data Security

- [ ] **Encryption at rest**: Yes / No (algorithm: _______)
- [ ] **Encryption in transit**: Yes / No (TLS version: _______)
- [ ] **Key management**: Describe _________________
- [ ] **Credential rotation**: Supported? Yes / No

### 6.3 Audit Logging

- [ ] **API calls logged**: Yes / No
- [ ] **Retention period**: _________________
- [ ] **Logs accessible to partners**: Yes / No
- [ ] **Log format**: _________________

### 6.4 Compliance

- [ ] **GDPR compliant**: Yes / No / N/A
- [ ] **CCPA compliant**: Yes / No / N/A
- [ ] **State-specific lending regulations**: How handled? _________________
- [ ] **Fair Credit Reporting Act (FCRA)**: Compliant? Yes / No
- [ ] **Equal Credit Opportunity Act (ECOA)**: Compliant? Yes / No

### 6.5 Penetration Testing

- [ ] **Frequency**: _________________
- [ ] **Third-party tested**: Yes / No
- [ ] **Vulnerability disclosure policy**: _________________

---

## Section 7: Support & Onboarding

### 7.1 Integration Support

- [ ] **Do you provide dedicated integration engineering support?** Yes / No
- [ ] **Support channel**:
  - [ ] Email
  - [ ] Slack (shared channel)
  - [ ] Phone
  - [ ] Ticket system
  - [ ] Other: _________________

- [ ] **Support hours**: _________________
- [ ] **Response time SLA**: _________________
- [ ] **Escalation process**: _________________

### 7.2 Onboarding Timeline

Typical integration timeline from kickoff to production:

- [ ] **Technical discovery**: _________ weeks
- [ ] **Development**: _________ weeks
- [ ] **Testing**: _________ weeks
- [ ] **Staging pilot**: _________ weeks
- [ ] **Production launch**: _________ weeks
- [ ] **Total estimated timeline**: _________ weeks/months

### 7.3 Sandbox Access

- [ ] **When can we get test credentials?** _________________
- [ ] **Sandbox limitations**: _________________
- [ ] **Test data**: Can you provide sample test cases? Yes / No
- [ ] **Webhook testing**: Supported in sandbox? Yes / No

### 7.4 Go-Live Process

What steps are required to move from test to production?

1. _________________
2. _________________
3. _________________

### 7.5 Ongoing Support

- [ ] **Production support SLA**: _________________
- [ ] **Account manager provided**: Yes / No
- [ ] **Quarterly business reviews**: Yes / No
- [ ] **Technical roadmap shared**: Yes / No

---

## Section 8: Business Terms (Optional)

*Note: These can be discussed separately with commercial teams*

### 8.1 Revenue Model

- [ ] **Revenue share**: _________ % (if applicable)
- [ ] **Referral fee**: _________ (flat or %)
- [ ] **Merchant discount rate (MDR)**: _________ %
- [ ] **Other fees**: _________________

### 8.2 Terms

- [ ] **Exclusivity requirements**: Yes / No
- [ ] **Minimum volume commitments**: Yes / No (describe: _______)
- [ ] **Co-marketing opportunities**: Yes / No
- [ ] **Branding requirements**: _________________

### 8.3 Merchant Economics

- [ ] **Average merchant fees**: $_________ or _________% per loan
- [ ] **Fee transparency**: Disclosed to merchant? Yes / No
- [ ] **Payment terms to merchant**: Net _________ days
- [ ] **Chargeback/clawback policy**: _________________

---

## Section 9: Additional Information

### 9.1 Existing Integrations

Do you have existing integrations with other platforms?

| Platform | Integration Type | Live Date | Notes |
|----------|------------------|-----------|-------|
| _________________ | (API/Portal/Other) | _________ | _________ |
| _________________ | (API/Portal/Other) | _________ | _________ |

### 9.2 References

Can you provide references from existing integration partners?

- [ ] Contact name: _________________
- [ ] Company: _________________
- [ ] Email: _________________

### 9.3 Additional Documentation

Please attach or link to any additional documentation:

- [ ] Integration guide / getting started
- [ ] API changelog
- [ ] Known issues or limitations
- [ ] Roadmap or upcoming features
- [ ] Case studies or success stories

---

## Submission Instructions

Please provide your responses by **[Date - 2 weeks from today]**.

**Preferred Format**:
- Complete this document with your responses
- Attach API documentation (Swagger/OpenAPI preferred)
- Attach any additional materials

**Submit to**:
- Email: partnerships@jobnimbus.com
- Subject: "Technical Integration RFI - [Provider Name]"

**Questions?**
Contact: [Your Name] at [email] or [phone]

---

## What Happens Next?

1. **Week 1-2**: You complete this RFI
2. **Week 3**: JobNimbus technical team reviews your submission
3. **Week 4**: Technical deep-dive call (2 hours) to discuss details
4. **Week 5**: JobNimbus provides decision on partnership and integration timeline

We aim to respond within **5 business days** of receiving your complete submission.

Thank you for your interest in partnering with JobNimbus!

---

**Document Version**: 1.0  
**Last Updated**: 2025-01-23  
**Owner**: Financing Platform Team

