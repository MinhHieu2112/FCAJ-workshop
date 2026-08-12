---
title: "Event 3"
date: 2026-07-25
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# FCAJ x Agentic AI Build Week: Show Up. Build. Pitch. WIN!

## Event overview

This session shared the experiences and outcomes of the teams participating in the Hackathon organized by the **AWS First Cloud AI Journey (FCAJ)** community in collaboration with **JI Fund**. The event brought together students, software engineers, and cloud and AI professionals to showcase their projects, exchange ideas, and discuss practical experiences in building AI-powered applications.

I attended the event to learn how different teams developed AI solutions within a limited timeframe and to gain a better understanding of the challenges involved in turning a prototype into a production-ready application.

## Objectives

- Learn about current trends in Agentic AI and its real-world applications.
- Understand how to build a Proof of Concept (PoC) that can evolve into a scalable solution.
- Learn from the experiences shared by Hackathon teams.
- Understand key considerations when deploying AI applications in production.

## Speakers

- **Mr. Nguyen Gia Hung** – Head of Solution Architect, AWS Vietnam; Founder of AWS First Cloud AI Journey.
- **Mr. Joseph Marazota** – Head of Technology, Amazon ASEAN.
- Participating Hackathon teams.

## Main content

### Overview of the Hackathon

Unlike traditional AI competitions, this Hackathon encouraged participants to develop AI Agents capable of planning tasks, using external tools, and completing complex workflows instead of only responding to user prompts like conventional Generative AI applications.

The evaluation focused not only on whether a Proof of Concept (PoC) worked correctly, but also on important production considerations such as implementing Guardrails, integrating Role-Based Access Control (RBAC), incorporating Human-in-the-loop mechanisms, and optimizing API costs.

During the 48-hour development period, each team designed and implemented a solution to solve a practical problem. Every team was given approximately five minutes to present its idea, system architecture, and product demonstration.

### Selected team presentations

Throughout the event, participating teams introduced various Agentic AI solutions for real-world problems. Each team presented its architecture, implementation approach, and future scalability.

#### 1. Agentic AI for online ordering

The team focused on improving the online ordering experience. According to the team, users often need to register an account, provide payment information, and navigate multiple menus before completing an order, resulting in unnecessary friction.

![Overview](/images/4-EventParticipated/Event_3/oneTeam.png)
***Figure 1. AWS architecture presented by the team.***

To address this problem, the team developed an AI Agent capable of assisting users through natural conversations. Key features included:

- Collecting restaurant menus from official websites through web scraping and storing the data on AWS.
- Implementing a memory mechanism so the AI could remember each user's previous orders and preferences.
- Automatically creating orders and adding items to the shopping cart during the conversation, reducing manual operations.

The presentation demonstrated how Agentic AI can act as a virtual assistant that completes user tasks instead of simply answering questions.

#### 2. Agentic AI for data analysis

Another team focused on helping Data Analysts automate repetitive reporting and data analysis tasks. The objective was to reduce manual work while supporting better decision-making.

![Overview](/images/4-EventParticipated/Event_3/Data-analysis.png)
***Figure 2. AWS architecture presented by the data analysis team.***

The proposed Proof of Concept (PoC) included the following capabilities:

- Receiving data analysis requests and generating initial reports.
- Implementing an Agent Loop that continuously improved reports based on analyst feedback.
- Applying Guardrails to validate generated results before presenting them to users.

The presentation showed how Agentic AI can collaborate with human analysts to improve both efficiency and report quality.

#### 3. Agentic AI for passenger traffic tracking

A third team focused on building a solution for tracking passenger foot traffic in corporate locations and airport facilities.

![Overview](/images/4-EventParticipated/Event_3/Guest.png)
***Figure 3. AWS architecture presented by the traffic tracking team.***

- The architecture utilizes Amazon Kinesis Video Streams to ingest camera feed imagery into the AWS processing pipeline.
- Data is processed using Amazon ECS, Amazon ECR, and Amazon SageMaker Endpoints to analyze video frames and identify passenger traffic metrics.
- Event results and analytical metrics are stored using Amazon S3 and Amazon DynamoDB.
- Amazon CloudFront, Amazon API Gateway, and AWS Lambda manage and process end-user requests.
- AgentCore Runtime combined with Amazon Bedrock supports building AI Agents capable of interacting with and analyzing stream data.
- Amazon Cognito, AWS IAM, AWS Secrets Manager, AWS CloudTrail, and Amazon CloudWatch manage authentication, authorization, security, and system observability.

Through this architecture, I gained a clearer picture of how diverse AWS services integrate into a unified end-to-end cloud platform.

## Knowledge gained

After attending the event, I learned several important lessons:

- An AI Agent should include planning, execution, and evaluation instead of relying on a single prompt.
- Enterprise AI applications require Guardrails to control the scope of AI operations and improve reliability.
- A demonstration product (PoC/MVP) differs significantly from a production system in terms of scalability, security, reliability, and operational cost.
- AI should simplify user workflows rather than simply adding more features.

## Application to my project

After the event, I identified several ideas that could improve my personal project:

- Integrate an AI Agent to help tenants report maintenance issues using natural language.
- Provide managers with AI-assisted queries for rental reports and property statistics.
- Combine Guardrails with Role-Based Access Control (RBAC) to restrict the information AI can access.
- Select appropriate AI models for different tasks to balance performance and operational cost.

## Personal experience

Through the presentations and discussions, I realized that building an AI application requires much more than implementing machine learning models. Security, scalability, reliability, and operational cost are equally important when moving from a prototype to production.

The event also gave me the opportunity to meet students and software engineers who share similar interests in AI and AWS. These conversations provided valuable insights into both technical learning and career development.

## Lessons learned

After attending the event, I drew several conclusions:

- Always start with real user problems before selecting technologies.
- Balance functionality, accuracy, and operational cost when designing AI applications.
- Design access control and data protection mechanisms from the beginning if AI is allowed to access system data.
- Participating in technical communities and Hackathons is an effective way to gain practical experience and stay up to date with emerging technologies.

## Photos

![FCAJ x Agentic AI Build Week Event](https://img.youtube.com/vi/hz32VBrvW7M/maxresdefault.jpg)

***Figure 4. Overview of the FCAJ x Agentic AI Build Week event.***

![Overview](/images/4-EventParticipated/Event_3/Meeting.jpeg)

***Figure 5. Group photo with the participating Hackathon teams.***

## Conclusion

The **FCAJ x Agentic AI Build Week** event provided valuable insights into how AI applications are designed, developed, and evaluated in practice. It helped me better understand the challenges involved in moving from an initial idea to a functional prototype and eventually to a production-ready system.

The knowledge and experiences gained from this event will help me continue improving my personal project, especially in exploring practical applications of AI within a SaaS property rental management system.