# Sunlight Financial API Requirements

## Overview

Integration requirements for Sunlight Financial Home API services. Sunlight Financial provides home improvement financing solutions with comprehensive project management, credit processing, and loan documentation capabilities.

**Base URL:** `https://test.connect.boomi.com/ws/rest/v1/pt/slf/`

**Documentation Reference:** https://apidocs.slfportal.com/

**Test Credentials:**
- Username: solarapidocs@sunlightfinancial.com
- Password: MkJPF6_qprKCMU*j

> **Important:** Always reference the v2 API documentation when implementing or updating Sunlight Financial integrations. All API calls are POST requests.

## Authentication & Authorization

### Double Authentication Method
Sunlight Financial uses a two-tier authentication system:
1. **Platform Basic Authentication** - Does not expire
2. **Sunlight Token** - 2-hour active life, must be refreshed after inactivity

### Token Authentication Endpoint
- **Endpoint:** `POST /authentication`
- **Content-Type:** `application/json`

#### Username/Password Method
```json
{
  "username": "user@example.com",
  "password": "password"
}
```

#### API Key Method  
```json
{
  "apiKey": "your_api_key_here"
}
```

## Core API Endpoints

### Project Management

#### Create Project
- **Endpoint:** `POST /applicant/create`
- **Description:** Create a project with an applicant without additional functionality
- **Required Fields:** 
  - `projectCategory`: Must be "Home"
  - `installStreet`, `installCity`, `installStateName`, `installZipCode`
  - `typeOfResidence`

#### List Products
- **Endpoint:** `POST /products/list` 
- **Description:** Retrieve loan products for your company
- **Required Fields:**
  - `productType`: Must be "Home"

#### Add Quote/Pricing
- **Endpoint:** `POST /pricing/createaccount`
- **Description:** Create pricing quotes and obtain monthly payment calculations
- **Key Features:**
  - Can create Project, Applicant, and Quote in single call
  - Automatic quote syncing
  - Support for promotional products

#### Quick Quote Calculator
- **Endpoint:** `POST /v2/pt/slf/calculator` 
- **Description:** Stateless API for quick loan amount and payment calculations
- **Note:** Uses v2 endpoint, results are not stored

### Credit Processing

#### Submit Credit
- **Endpoint:** `POST /credit/submit`
- **Description:** Run credit application for one or two applicants
- **Methods:**
  - Synchronous (fully hosted)
  - Asynchronous (email credit)  
  - Asynchronous (credit URL)
- **Required:** `isCreditAuthorized: true`

#### Credit Syncing
- **Endpoint:** `POST /object/credit`
- **Description:** Manage active credit applications (only one can be syncing)

### Loan Documents

#### Request Loan Documents
- **Endpoint:** `POST /loandocs/send`
- **Description:** Send loan package via DocuSign to applicant(s)

#### In-Person Loan Documents  
- **Endpoint:** `POST /loandocs/send` (with `deliveryMethod: "inPerson"`)
- **Description:** Generate URL for in-browser DocuSign signing session

### Status Management

#### Status Pull
- **Endpoint:** `POST /status/pull`
- **Description:** Get current status and data for up to 25 projects
- **Parameters:** Use `projectIds` or `externalIds` (not both)

#### Event-Based Status
- **Description:** Real-time webhook notifications for project events
- **Events:** Credit approvals/declines, document signing, project completion, etc.

### Disclosure Management

#### General Disclosures
- **Endpoint:** `POST /disclosures/get`
- **Description:** Retrieve required legal disclosures for display
- **Types:** CCPA, Privacy Policy, Phone, Credit Consent, Terms & Conditions

## Data Models

### Project Model
```json
{
  "externalId": "string",
  "apr": "number",
  "term": "integer", 
  "isACH": "boolean",
  "productType": "string (Home)",
  "projectCategory": "string (Home)",
  "typeOfResidence": "string (Own/Rent)",
  "installStreet": "string",
  "installCity": "string", 
  "installStateName": "string",
  "installZipCode": "string",
  "applicants": ["array of applicant objects"],
  "quotes": ["array of quote objects"]
}
```

### Applicant Model
```json
{
  "isPrimary": "boolean",
  "firstName": "string",
  "middleInitial": "string",
  "lastName": "string", 
  "phone": "string (10 digits)",
  "otherPhone": "string (10 digits)",
  "email": "string",
  "mailingStreet": "string",
  "mailingCity": "string",
  "mailingStateName": "string", 
  "mailingZipCode": "string (5 digits)",
  "residenceStreet": "string",
  "residenceCity": "string",
  "residenceStateName": "string",
  "residenceZipCode": "string (5 digits)",
  "dateOfBirth": "string (YYYY-MM-DD)",
  "ssn": "string (9 digits)",
  "annualIncome": "integer",
  "employerName": "string",
  "jobTitle": "string",
  "employmentYears": "string",
  "employmentMonths": "string"
}
```

### Quote Model
```json
{
  "loanAmount": "number",
  "finalMonthlyPayment": "number",
  "finalEscalatedMonthlyPayment": "number", 
  "monthlyPayment": "number",
  "escalatedMonthlyPayment": "number",
  "paymentCountPromo": "integer",
  "paymentCountStandard": "integer",
  "isSyncing": "boolean"
}
```

### Unified JSON Response
```json
{
  "returnCode": "string",
  "projects": [{
    "id": "string",
    "hashId": "string", 
    "projectStatus": "string",
    "projectStatusDetail": "string",
    "applicants": ["array"],
    "credits": ["array"],
    "quotes": ["array"],
    "stips": ["array"]
  }]
}
```

## Implementation Requirements

### Core Features
- [ ] Project creation and management
- [ ] Applicant data collection and validation
- [ ] Quote generation and syncing
- [ ] Credit application processing (synchronous/asynchronous)
- [ ] Loan document generation and signing
- [ ] Status monitoring and event handling
- [ ] Required disclosure presentation

### Data Management  
- [ ] Secure PII handling (never store SSN, DOB in your system)
- [ ] External ID mapping for project tracking
- [ ] Quote syncing management (only one active at a time)
- [ ] Credit syncing management (only one active at a time)

## Non-Functional Requirements

### Performance
- [ ] Token refresh handling (2-hour expiry)
- [ ] Efficient pagination for large result sets
- [ ] Real-time status updates via webhooks

### Security
- [ ] Double authentication implementation
- [ ] HTTPS communication only
- [ ] PII data protection and compliance
- [ ] API key secure storage

### Reliability
- [ ] Token expiration handling and refresh
- [ ] Error handling for all API responses
- [ ] Fallback strategies for API unavailability
- [ ] Event-based status monitoring

## Dependencies

- [ ] Sunlight Financial API access and credentials
- [ ] DocuSign integration for loan documents
- [ ] Webhook endpoint for status events
- [ ] Test environment access: Platform Basic Auth + API credentials
- [ ] Production environment credentials (separate from test)

## Testing Requirements

### Test Data Available
- [ ] Automated test credit approvals (Jonathan Consumer, SSN: 999999990, Income: 10001)
- [ ] Specific decline scenarios (Income variations: 11024, 11027, 11025)
- [ ] Comprehensive test applicant data for various states and outcomes
- [ ] Forced approval/decline scenarios for development testing

### Testing Categories
- [ ] Unit tests for API request/response handling
- [ ] Integration tests with Sunlight staging environment  
- [ ] End-to-end testing for complete loan workflow
- [ ] Error scenario testing (expired tokens, invalid data)
- [ ] Disclosure compliance testing

## Key Business Rules

### General Rules
- All API calls are POST requests
- If no object ID provided, new object is created
- If existing ID provided with updates, object is updated
- If existing ID provided without data, existing object data is used

### External ID Usage
- Use your own system IDs instead of Sunlight IDs
- First time external ID creates new record
- Subsequent calls with same external ID reference existing record

### Credit and Quote Syncing
- Only one credit can be "syncing" (active) at a time
- Only one quote can be "syncing" (active) at a time  
- Cannot sync after loan documents are signed (requires Change Order)

### Required Disclosures
- Must present specific disclosures at designated form locations
- HTML formatting support required for disclosure text
- CCPA disclosure mandatory for California residents
- Spanish translation support available

## Spanish Language Support

### Features
- Complete loan document translation
- DocuSign interface in Spanish
- Email communications in Spanish
- Sunlight user interface in Spanish

### Requirements
- Set `language: "Spanish"` on project creation (one time only)
- Display English disclosures with Spanish translation links
- Add required Spanish notice on decision screens
- Use Disclosure API for translated text (`spanishDisclosureText`)

## Reference Information

### Project Status Values
- "Pending Loan Docs" - Valid credit approval, documents not signed
- "Payment Stage" - Approved for payment requests
- "Payment In Process" - Draw request approved
- "Project Completed" - Final funding completed
- "Declined"/"Withdrawn" - End states

### Credit Status Values
- "New" - Credit not sent to bureau
- "Auto Approved" - System approved
- "Auto Decline" - System declined
- "Pending Review" - Manual review required
- "Expired" - Application expired

### Product Types
- "HIS" - Home Improvement Same-as-Cash
- "HIN" - Home Improvement No Interest
- "HII" - Home Improvement Installment

### Stipulation Codes (Common)
- TTL - Title/deed verification required
- VOI - Income verification required  
- VOM - Mortgage statement required
- ACH - Bank account validation needed
- GID - Government ID required