---
title: "Event 1"
date: 2026-06-27
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# FCAJ Community Day - June 2026

## Event overview

- **Event:** FCAJ Community Day - June 2026
- **Organizer:** AWS Study Group / FCAJ Community
- **Main topics:** Career trends in Cloud and AI Engineering, AI-driven FinOps and Cloud Security, and the architecture of Amazon Q Business and Model Context Protocol (MCP) Server.

---

## 1. Career trends in the Agentic AI era

### Current situation and challenges

- **Rapid AI adoption:** AI agents and AI coding assistants are significantly improving software development productivity. As a result, many companies are raising recruitment standards and placing greater emphasis on candidates who can effectively use AI tools.
- **Infrastructure complexity:** As cloud systems grow, managing infrastructure becomes increasingly complex. AI alone cannot fully understand the complete context of source code, cloud infrastructure, and business logic within large enterprise systems.

### Key take-aways

- **AI will not completely replace engineers:** Roles such as **Cloud Engineer**, **DevOps Engineer**, **Solution Architect**, and **Site Reliability Engineer** remain essential because they require practical experience, incident response, architectural decision-making, and business understanding.
- **Develop AI collaboration skills:** Rather than viewing AI as a replacement, engineers should learn how to use AI to automate repetitive tasks while focusing on architecture design, optimization, and operational reliability.

---

## 2. AI applications in FinOps and cloud security

### AI for FinOps

- **Current challenge:** Finance teams often lack technical knowledge of cloud services, while cloud engineers may not fully understand financial cost management.
- **Proposed solution:** AI can analyze AWS billing data, detect abnormal spending patterns, and recommend cost optimization strategies, helping organizations improve their FinOps practices.

### AI for cloud security

- **Current challenge:** Security issues are sometimes overlooked or discovered too late, allowing vulnerabilities to remain in production systems for extended periods.
- **Proposed solution:** AI Agents can automate several security-related tasks, including:
  - Performing security assessments.
  - Reviewing Infrastructure as Code (IaC) configurations.
  - Supporting penetration testing activities.
  - Continuously analyzing system logs to identify potential threats.

---

## 3. Cost considerations for private AI infrastructure (Amazon Q Business and MCP Server)

### Infrastructure architecture and estimated costs

When deploying enterprise AI solutions such as **Amazon Q Business** or an **MCP Server** inside a private AWS Virtual Private Cloud (VPC), organizations should consider the infrastructure costs required to maintain a secure environment.

| Infrastructure component | Estimated monthly cost (USD) | Purpose |
| :--- | :---: | :--- |
| **Route 53 Resolver** | ~$180 | DNS resolution for private endpoints |
| **Application Load Balancer (ALB)** | ~$32 | Routing requests to internal services |
| **EC2 instances** | Depends on instance type | Hosting MCP Server and supporting services |
| **AWS Secrets Manager** | Based on the number of secrets | Secure storage of API keys and credentials |
| **Data transfer** | Usage-based | Network traffic between AWS services |

> **Estimated fixed infrastructure cost:** Approximately **USD 250–350 per month**, excluding actual AI model usage and data processing costs.

### Lessons learned about cost estimation

- **Hidden infrastructure costs:** Many AI projects focus only on LLM or API pricing while overlooking the cost of supporting infrastructure such as Route 53 Resolver, Application Load Balancer, VPC networking, and Secrets Manager.
- **Capacity planning:** Infrastructure costs should be estimated based on the expected number of users and projected monthly data transfer before deploying the solution in production.

## 4. Event photos
![Overview](/images/4-EventParticipated/Event_1/Meeting.jpeg)
***Figure 1. Group photo at the FCAJ Community Day - June 2026 event.***