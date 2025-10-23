# Financing API Documentation

This repository contains comprehensive API integration requirements and documentation for building JobNimbus's multi-provider financing platform.

## Quick Start

1. **Clone this repository:**
   ```bash
   git clone https://github.com/jess-talbert/financing-api-docs.git
   cd financing-api-docs
   ```

2. **Open in Cursor:**
   ```bash
   cursor .
   ```

## 📚 Documentation Overview

### Implementation & Planning

- **[Multi-Provider Financing Platform Plan](./Multi-Provider-Financing-Platform-Plan.md)** - 18-month roadmap (Phase 0-5)
- **[Implementation Summary](./IMPLEMENTATION-SUMMARY.md)** - How to use all documents and track progress

### Partner Vetting Framework (Phase 0)

- **[Partner Qualification Checklist](./partner-qualification-checklist.md)** - Phase 1: 30-minute screening
- **[Technical Integration RFI](./technical-integration-rfi.md)** - Phase 2: Documentation request
- **[Technical Evaluation Scorecard](./technical-evaluation-scorecard.md)** - Phase 3: Scoring rubric
- **[Partner Integration Guide](./partner-integration-guide.md)** - Provider-facing requirements

### API Documentation

- **[Financing API Requirements](./Financing%20API%20Requirements.md)** - Multi-provider API documentation (FinTurf, WiseTack, Stripe-Affirm)
- **[Sunlight Financial API Requirements](./Sunlight%20Financial%20API%20Requirements.md)** - Complete Sunlight API docs

### Customer Flow & Design

- **[FinTech Marketplace Customer Flow](./FinTech-Marketplace-Customer-Flow.md)** - Customer journey visualization and frontend integration

## Financing Providers Covered

| Provider | Use Case | Documentation |
|----------|----------|---------------|
| **FinTurf** | Merchant offer stacks, loan estimations | https://docs.finturf.com/#7f9c019c-f728-4ae2-94b6-cb7953ab147a |
| **Sunlight Financial** | Home improvement project financing | https://apidocs.slfportal.com/ |
| **WiseTack** | Point-of-sale financing, merchant onboarding | https://wisetack-sec.us/api/index.html#section/Introduction |
| **Stripe-Affirm** | Contractor payment processing with financing | https://stripe.com/docs/payments/affirm |

## 🚀 How to Use This Repository

### For Product Managers

1. **Start Here:** [Multi-Provider Financing Platform Plan](./Multi-Provider-Financing-Platform-Plan.md)
2. **Understand Phase 0:** [Implementation Summary](./IMPLEMENTATION-SUMMARY.md)
3. **Begin Partner Vetting:** Use the 3-phase vetting framework
   - [Partner Qualification Checklist](./partner-qualification-checklist.md) - Screen 40+ providers
   - [Technical Integration RFI](./technical-integration-rfi.md) - Request documentation
   - [Technical Evaluation Scorecard](./technical-evaluation-scorecard.md) - Score and classify

### For Partnership Team

1. **Send to Providers:** [Partner Integration Guide](./partner-integration-guide.md)
2. **Use Screening Tool:** [Partner Qualification Checklist](./partner-qualification-checklist.md)
3. **Track Progress:** Maintain provider tracking spreadsheet (see Implementation Summary)

### For Engineering Team

1. **Review API Standards:** [Multi-Provider Financing Platform Plan](./Multi-Provider-Financing-Platform-Plan.md) - Section 2B
2. **Study Existing Providers:**
   - [Sunlight Financial API Requirements](./Sunlight%20Financial%20API%20Requirements.md)
   - [Financing API Requirements](./Financing%20API%20Requirements.md) (WiseTack, FinTurf, Stripe-Affirm)
3. **Understand Architecture:** Plan document - Phase 0, Section 2 (Platform Architecture)

### For Design Team

1. **Review Customer Journey:** [FinTech Marketplace Customer Flow](./FinTech-Marketplace-Customer-Flow.md)
2. **Understand Integration Points:** Plan document - Product Integration Points
3. **Reference Figma:** Finance Partner Card designs (linked in plan)

## 📋 Phase 0 Workflow

**Week 1-4: Initial Screening**
1. Contact 40+ financing providers
2. Use [Partner Qualification Checklist](./partner-qualification-checklist.md) for 30-min calls
3. Send [Partner Integration Guide](./partner-integration-guide.md) to qualified providers

**Week 5-8: Technical Review**
1. Send [Technical Integration RFI](./technical-integration-rfi.md) to Green/Yellow Light providers
2. Review API documentation submissions
3. Test sandbox environments

**Week 9-12: Deep Dive Evaluation**
1. Use [Technical Evaluation Scorecard](./technical-evaluation-scorecard.md) to score providers
2. Classify into Tier 1/2/3
3. Send approval/rejection emails (templates in scorecard)

**Output:** 6-8 Tier 1 partners ready for Phase 1 integration

## Contributing

When updating documentation:
1. Make changes to the relevant sections
2. Test any code examples
3. Update the Quick Reference Guide if adding new providers
4. Commit changes with descriptive messages

## Support

For questions about specific provider implementations, refer to the individual provider documentation URLs listed above.