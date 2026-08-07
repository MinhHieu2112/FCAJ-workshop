---
title: "Week 6 Worklog"
date: 2026-07-27
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

* Develop **message** module: build real-time chat functionality between tenants and managers using WebSockets.
* Build property-specific chat user interfaces and persist conversation histories in the database.
* Develop **notification** module: create in-app notification mechanisms for tenants and managers.
* Integrate **Amazon SES** service to send automated emails when rental application or lease statuses change.
* Test real-time messaging flows and email notification delivery via Amazon SES.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Develop WebSocket Gateway for **message** module: <br>&emsp; + Initialize WebSocket Gateway using NestJS WebSockets (`@nestjs/websockets`) <br>&emsp; + Build middleware to authenticate WebSocket handshake connections using JWT tokens <br>&emsp; + Manage active socket instances for tenants and managers | 27/07/2026 | <https://docs.nestjs.com/websockets/gateways> |
| Tue | - Complete Real-time Chat functionality: <br>&emsp; + Define Prisma model for Message and build API to fetch conversation history by `propertyId` <br>&emsp; + Build real-time chat widget UI in Next.js frontend <br>&emsp; + Handle instant message emission/reception events and auto-scroll to latest messages | 28/07/2026 | <https://socket.io/docs/v4/> |
| Wed | - Integrate **Amazon SES** service for automated email delivery: <br>&emsp; + Configure Amazon SES (Simple Email Service) on AWS Console and verify sender email <br>&emsp; + Build `SesService` in NestJS using AWS SDK v3 `SendEmailCommand` <br>&emsp; + Create HTML email templates: rental application submitted, application approved/denied | 29/07/2026 | <https://docs.aws.amazon.com/ses/> |
| Thu | - Develop **notification** module (in-app notifications): <br>&emsp; + Define Prisma model for Notification and status event types (APPLICATION_SUBMITTED, APPLICATION_APPROVED, LEASE_CREATED) <br>&emsp; + Build APIs to fetch notifications and mark items as read <br>&emsp; + Display unread notification count badge on the main navbar | 30/07/2026 | |
| Fri | - Test messaging and notification workflows: <br>&emsp; + Test real-time chat between tenant and manager across different browser sessions <br>&emsp; + Handle automatic reconnection logic upon WebSocket disconnection <br>&emsp; + Verify actual email delivery from Amazon SES upon rental application approval operations | 31/07/2026 | |

---

### Week 6 Achievements:

* Completed **Message module**: supported real-time messaging between tenants and managers via WebSockets secured with JWT authentication.

* Successfully built **real-time chat interface** in Next.js frontend, accurately organizing conversation history per property listing.

* Integrated **Amazon SES** successfully: the system automatically sends styled HTML transactional email notifications upon application approval or lease generation.

* Completed **Notification module**: provided an in-app notification system with read/unread tracking.

* Completed messaging flow testing: ensured smooth real-time chat transmission and reliable email delivery via Amazon SES.

---

### Knowledge / Experience Gained:

* Mastered WebSocket architecture in NestJS Gateways: distinguishing stateful WebSocket connections from stateless HTTP REST APIs.
* Understood how to authenticate WebSocket connections via JWT bearer tokens during initial connection handshakes.
* Learned how to integrate AWS SDK v3 for Amazon SES to send transactional emails in a backend application.
* Gained frontend real-time chat implementation skills: managing messages with React state, auto-scrolling UI, and handling socket connection states.
