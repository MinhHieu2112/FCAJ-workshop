---
title: "Event 2"
date: 2026-07-04
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Enterprise cloud architectures and industry application

### Event objectives

- Help students understand how enterprises design and operate systems on AWS while providing insights into industry hiring requirements and career opportunities in cloud computing.
- Provide an overview of the current job market, practical technical expectations for junior cloud engineers, and effective ways to access career opportunities.
- Continue the *"Pay it Forward"* spirit of the AWS First Cloud AI Journey community, where experienced members return to share their knowledge, provide guidance, and support the next generation.

### Speakers

- **Mr.Nguyen Gia Hung** - Head of Solution Architect, AWS Việt Nam (Founder của AWS First Cloud AI Journey)
- **Mr.Khang Nguyen** - Solution Architect, Cloud Kinetics
- **Ms.Nhu Tran** - Account Manager, Amazon Web Services Việt Nam
- **Mr.Vinh Banh** - Senior Data Engineer, Renova Cloud
- Solution Architects and representatives from AWS partners **Cloud Kinetics** and **Renova Cloud**

### Key topics

#### Current cloud hiring trends

- **Rising entry requirements:** Expectations for intern and fresher positions are becoming increasingly demanding. Even internship candidates are expected to have a solid understanding of containerization, orchestration (such as Kubernetes), and networking fundamentals.
- **Migration of core systems to the cloud:** Organizations are moving core banking, finance, insurance, and e-commerce systems to the cloud, requiring engineers to understand security, high availability, and scalability.

#### Understanding the hidden job market

- **Hiring channels:** Approximately **90%–100%** of specialized cloud job opportunities are not advertised on public recruitment platforms.
- **Internal referrals:** Many positions are filled through employee referrals, professional networks, and community connections.
- **Community engagement:** Actively participating in technical communities is an effective way to discover valuable career opportunities.

#### Enterprise cloud architecture trends

- Large enterprises are expanding from small-scale cloud adoption to deploying their core infrastructure on AWS.
- System architectures must be designed with a strong focus on data security, cost optimization, and efficient resource allocation.

### What I learned

#### Technical mindset and engineering skills

- From the speakers' presentations, I realized that fundamental knowledge such as Linux, networking, Docker, Kubernetes, and Infrastructure as Code remains essential for cloud engineers. These foundations should be mastered before exploring more advanced AWS services.
- When solving complex technical problems, it is important to make reasonable assumptions, refine the problem scope, and ask focused questions so that others can provide effective guidance.

#### Career development

- **Build a professional presence:** Technical skills should be complemented by active networking. Building credibility within the community can create long-term career opportunities.
- **Consistency matters:** Career growth is the result of continuous learning and persistent effort rather than luck.
- **Cross-functional collaboration:** Real-world projects require close cooperation between engineering teams and business departments to deliver solutions that meet business needs.

### Applying the knowledge to my project

- **Optimizing image storage:** After attending the event, I decided to separate image storage from the application server by using Amazon S3. This allows the backend to focus on business logic instead of handling large media files.
- **Location-based features:** For the property management module, I integrated Amazon Location Service to support address search and interactive maps instead of relying on third-party mapping services.
- **Centralized authentication and authorization:** I implemented Amazon Cognito User Pool to manage user identities and issue JWTs, then integrated NestJS Guards to enforce role-based access control (RBAC) in the Next.js application.
- **Scalable infrastructure:** I planned to containerize the application with Docker as a foundation for future container orchestration and horizontal scaling.

### Event experience

This was my first time attending a technical event at the AWS Vietnam office. The atmosphere was welcoming, and the speakers spent a considerable amount of time discussing real-world projects and sharing career advice with students.

#### Learning from industry experts

- I had the opportunity to learn directly from Mr. Nguyen Gia Hung and solution architects from Cloud Kinetics and Renova Cloud about designing cloud infrastructure for enterprise-scale systems.
- Their presentations provided practical insights into the skills and knowledge expected of cloud engineers today.

#### Networking and community engagement

- The open discussion sessions encouraged students to ask questions, exchange technical ideas, and seek career advice.
- One of the most impressive aspects of the event was the **Pay it Forward** spirit of the AWS First Cloud AI Journey community. Many former participants returned to share their experiences and mentor new students.

#### Key takeaways

- Building a long-term career requires continuous learning beyond university courses, especially in areas such as Kubernetes, CI/CD, and serverless computing.
- I also realized that technical knowledge alone is not enough. Actively participating in professional communities, expanding my network, and maintaining a continuous learning mindset are equally important for future career development.

#### Event photos

![AWS Study Tour Event](https://img.youtube.com/vi/FKtMkUqyny4/maxresdefault.jpg)

***Figure 1. Overview of the "Enterprise cloud architectures and industry application" study tour at the AWS Vietnam office.***

![Overview](/images/4-EventParticipated/event_2.jpeg)

***Figure 2. Participating in the study tour at the AWS Vietnam office.***

> Overall, the event strengthened my understanding of AWS technologies while helping me shape the system design mindset for my project. It also broadened my perspective on professional development and future career opportunities.