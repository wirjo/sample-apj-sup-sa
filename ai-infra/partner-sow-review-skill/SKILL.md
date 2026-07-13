---
name: partner-sow-review
description: Review partner Scope of Work (SoW) documents against the AWS Well-Architected Framework before submitting for Partner Funding approvals (AWS credits, POC funds). Catches red flags around cost-architecture misalignment and use-case-architecture misalignment. Use when a partner asks to review a SoW, validate architecture costs, check funding eligibility, or assess alignment between proposed workload and AWS services.
---

# Partner SoW Review

Review a partner's Scope of Work (SoW) against the AWS Well-Architected Framework
to catch red flags before submitting for Partner Funding approvals.

## Why this skill exists

Partners submit Scope of Work documents to obtain AWS funding (credits, POC funds,
Migration Acceleration Program funds, etc.). The AWS Calculator cost breakdown in
the SoW is what determines funding amounts. Common issues that delay or block
approvals:

- Cost estimates that don't match the architecture (e.g., pricing EC2 instances
  that aren't in the architecture diagram, or missing costs for services that are)
- Architecture that doesn't match the stated use case (e.g., proposing a
  data lake architecture for a simple web app, or over-engineering a POC)
- Well-Architected anti-patterns that indicate the partner hasn't thought
  through reliability, security, or cost optimization

This skill helps partners self-service their SoW reviews before submission,
catching these issues early and reducing approval cycle time.

## Prerequisites check

The user should provide:
1. The SoW document (PDF, Word, or pasted text)
2. The AWS Calculator estimate (URL or exported CSV/JSON)
3. (Optional) Architecture diagram

If any of these are missing, ask the user to provide them.

## Review process

### Step 1: Understand the SoW

Extract and summarize:
- **Customer name and use case** — What problem is being solved?
- **Proposed architecture** — What AWS services are involved?
- **Timeline** — How long is the engagement?
- **Funding requested** — How much and what type (credits, POC, MAP)?
- **Cost estimate** — What does the AWS Calculator breakdown show?
- **Success criteria** — How will the project be measured?

### Step 2: Cost-Architecture Alignment Check

This is the primary red flag area. Compare the cost estimate against the
architecture and flag mismatches:

**Red flags to catch:**

| Issue | Example | Risk |
|-------|---------|------|
| Service in cost estimate not in architecture | Paying for Redshift but architecture shows only S3 + Athena | Inflated costs → funding rejection |
| Service in architecture not in cost estimate | Architecture shows ALB + ECS but cost estimate only has EC2 | Under-estimated → project failure |
| Instance sizing mismatch | Architecture says "small POC" but costs show p5.48xlarge production cluster | Over-scoped for stated purpose |
| Region mismatch | Architecture says ap-southeast-2 but costs calculated for us-east-1 | Inaccurate pricing |
| Missing data transfer costs | Multi-region or hybrid architecture with no transfer line items | Commonly under-estimated 30-50% |
| Reserved/Savings Plans in POC | Cost estimate uses 1yr RI pricing for a 3-month POC | Misleading cost basis |
| No cost for supporting services | Main compute priced but no CloudWatch, VPC, NAT Gateway, S3 storage | 15-30% cost undercount |

**How to validate:**

```bash
# If the user provides an AWS Calculator URL, fetch and parse it
# Calculator URLs look like: https://calculator.aws/estimate?id=xxxxx
```

Cross-reference every line item in the cost estimate against the architecture
description. Flag any service that appears in one but not the other.

### Step 3: Use-Case-Architecture Alignment Check

Assess whether the proposed architecture is appropriate for the stated use case:

**Red flags to catch:**

| Issue | Example | Risk |
|-------|---------|------|
| Over-engineering | POC for a chatbot using EKS + Kafka + Neptune when Bedrock + Lambda would suffice | Unnecessary complexity, higher cost |
| Under-engineering | Production ML platform with no auto-scaling, single-AZ, no monitoring | Reliability/operational risk |
| Wrong service choice | Using EC2 self-managed for a stateless API when Lambda/Fargate is cheaper | Cost inefficiency |
| Lift-and-shift only | "Modernization" SoW that's just re-hosting VMs with no architectural improvement | Doesn't justify funding |
| Missing Well-Architected pillars | No mention of security (IAM, encryption), reliability (multi-AZ), or operations (monitoring) | Approval likely flagged |
| Scope creep indicators | SoW covers 15 services for a 4-week engagement | Unrealistic timeline |

**Well-Architected Framework alignment:**

For each pillar, check if the SoW addresses it:

1. **Operational Excellence** — Is there monitoring, alerting, runbooks?
2. **Security** — IAM roles, encryption at rest/transit, network isolation?
3. **Reliability** — Multi-AZ, auto-scaling, backup/recovery?
4. **Performance Efficiency** — Right-sized instances, caching, CDN?
5. **Cost Optimization** — Right pricing model, auto-scaling down, lifecycle policies?
6. **Sustainability** — Efficient resource use, right-sized?

A POC doesn't need all of these, but a production deployment should address most.
Flag missing pillars relative to the stated environment (POC vs production).

### Step 4: Funding Reasonableness Check

Assess whether the funding request is reasonable:

- **POC funding** (typically $1K–$25K): Should be 1–3 months, focused scope,
  clear success criteria, minimal production-grade requirements
- **Migration funding** (MAP): Should show clear migration path, TCO comparison,
  business case for each workload
- **Credits for production**: Should show customer commitment, architecture
  maturity, operational readiness

**Red flags:**
- Requesting $50K in credits for a 2-week POC
- No clear success criteria (how do you know the POC succeeded?)
- Cost estimate significantly different from requested funding amount
- SoW that's really a training engagement disguised as implementation

### Step 5: Generate report

Present findings as:

```
## SoW Review Summary

**Customer:** [name]
**Use Case:** [one-line summary]
**Funding Requested:** [amount and type]
**Overall Assessment:** ✅ Ready / ⚠️ Needs revision / ❌ Significant concerns

## Cost-Architecture Alignment
[findings with specific line items]

## Use-Case-Architecture Alignment
[findings with WAF pillar assessment]

## Funding Reasonableness
[assessment]

## Recommendations
1. [specific action to fix issue]
2. [specific action to fix issue]
...
```

## Important notes

- This is a **self-service pre-review** — it does not replace the actual
  AWS approval process
- Be specific: cite exact services, costs, and page numbers from the SoW
- Focus on the two primary red flag areas: cost-architecture alignment and
  use-case-architecture alignment
- The AWS Calculator cost breakdown is the key funding determinant — any
  discrepancy between it and the architecture is the #1 blocker
- When in doubt, suggest the partner contact their Partner Development Manager
  (PDM) or Partner Solutions Architect (PSA)
