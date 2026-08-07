---
title: "Week 5 Worklog"
date: 2026-07-20
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Complete the manager dashboard user interface in the Next.js application.
* Build property editing functionality: update property details, upload new images, or remove old images on Amazon S3.
* Migrate the database from the local development environment to **Amazon RDS (PostgreSQL + PostGIS managed)**.
* Fix system bugs and optimize database connections and query performance.
* Report progress and demo completed features to the mentor.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Complete Manager Dashboard UI: <br>&emsp; + Build property portfolio management page with status tabs (Available, Rented, Pending) <br>&emsp; + Build rental application review table for incoming applications <br>&emsp; + Display quick summary cards: active listings, new applications, active leases | 20/07/2026 | <https://nextjs.org/docs> |
| Tue | - Build property editing functionality: <br>&emsp; + Build property edit form in frontend with full property fields: price, area, amenities, location <br>&emsp; + Build image management logic: support uploading new images to Amazon S3 or deleting obsolete photos <br>&emsp; + Integrate NestJS backend `PATCH /properties/:id` API | 21/07/2026 | <https://docs.aws.amazon.com/s3/> |
| Wed | - Migrate database to Amazon RDS: <br>&emsp; + Provision Amazon RDS PostgreSQL instance (db.t3.micro, single-AZ) via AWS Console <br>&emsp; + Configure Security Group rules allowing secure access from backend IP <br>&emsp; + Install PostGIS extension on Amazon RDS PostgreSQL <br>&emsp; + Run `prisma db push` / `prisma migrate deploy` to push schema and seed data onto RDS | 22/07/2026 | <https://docs.aws.amazon.com/rds/> |
| Thu | - Bug fixes and system performance optimization: <br>&emsp; + Configure connection pooling for Prisma connecting to Amazon RDS <br>&emsp; + Resolve edge-case exceptions when updating property listing and application statuses <br>&emsp; + Benchmark cloud database latency and optimize slow database queries | 23/07/2026 | <https://www.prisma.io/docs/guides/performance-and-optimization> |
| Fri | - Test Manager Dashboard and Amazon RDS deployment: <br>&emsp; + Test end-to-end flow: manager creates listing → edits listing → manages S3 images → approves application on Amazon RDS database <br>&emsp; + Evaluate Week 5 progress with mentor and record feedback for Week 6 optimizations | 24/07/2026 | |

---

### Week 5 Achievements:

* Completed **Manager Dashboard UI**: provided an intuitive interface for property portfolio management, application tracking, and lease monitoring.

* Completed **Property Editing Functionality**: supported flexible updates to property details while synchronizing image additions and deletions on Amazon S3.

* Successfully migrated database to **Amazon RDS (PostgreSQL + PostGIS)**: established a managed, cloud-hosted relational database environment on AWS.

* Resolved concurrent data update issues and successfully configured Prisma connection pooling for Amazon RDS.

* Successfully demoed Week 5 progress to the mentor, confirming that property management workflows operate seamlessly on Cloud RDS.

---

### Knowledge / Experience Gained:

* Learned how to provision and configure cloud-managed Amazon RDS PostgreSQL instances, configuring Security Groups according to the principle of least privilege.
* Mastered enabling and managing the PostGIS extension on Amazon RDS PostgreSQL for spatial data queries.
* Gained experience handling complex edit forms while managing synchronized image uploads and deletions on Amazon S3.
* Learned database connection pool optimization techniques when transitioning from local DB to cloud-managed databases.
