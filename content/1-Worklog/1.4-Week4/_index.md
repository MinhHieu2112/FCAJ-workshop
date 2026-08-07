---
title: "Week 4 Worklog"
date: 2026-07-13
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Develop **property** module: build CRUD APIs for listings and integrate Amazon S3 for image upload and storage.
* Build multi-criteria property search and filtering functionality (price, area, bedrooms, amenities, property type).
* Complete user settings and profile pages for tenants and managers in the Next.js frontend.
* Integrate property listing and search interfaces on frontend with NestJS backend APIs.
* Test and optimize all features implemented during the week.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Develop backend **property** module: <br>&emsp; + Define Prisma models for Property, Location, PropertyImage, and create CRUD DTOs <br>&emsp; + Integrate AWS SDK v3 for **Amazon S3**: build multi-image upload service and store image URLs <br>&emsp; + Build APIs for creating, updating, and deleting property listings | 13/07/2026 | <https://docs.aws.amazon.com/s3/> |
| Tue | - Build property search and multi-criteria filtering: <br>&emsp; + Write dynamic Prisma query logic to filter by price range, area, bedrooms, bathrooms, and property type <br>&emsp; + Build amenity filter (wifi, AC, parking, furniture) <br>&emsp; + Implement pagination and sorting for property list APIs | 14/07/2026 | <https://www.prisma.io/docs/> |
| Wed | - Complete user settings pages in Next.js frontend: <br>&emsp; + Build account Settings and Profile pages for tenants and managers <br>&emsp; + Build forms for updating personal information and profile avatar <br>&emsp; + Manage authentication state on client using Redux Toolkit | 15/07/2026 | <https://nextjs.org/docs> |
| Thu | - Integrate search and listing frontend interfaces: <br>&emsp; + Build Property List page with search bar and filter sidebar <br>&emsp; + Build Property Detail page displaying S3 image gallery, amenities, and description <br>&emsp; + Connect APIs via RTK Query, attaching JWT bearer token in all requests | 16/07/2026 | <https://redux-toolkit.js.org/> |
| Fri | - Test and optimize implemented features: <br>&emsp; + Test property creation flow with simultaneous S3 image uploads <br>&emsp; + Test filter accuracy under combined search conditions <br>&emsp; + Verify responsive UI design across mobile and desktop, fixing layout bugs | 17/07/2026 | |

---

### Week 4 Achievements:

* Fully developed **Property module**: provided complete CRUD APIs for property management and image metadata handling.

* Integrated **Amazon S3** successfully: enabled multi-image uploads for property listings and seamless image rendering via S3 bucket URLs.

* Built **multi-criteria search and filter functionality**: supported filtering by rental price, area, bedroom count, amenities, and property type.

* Completed **Settings and Profile pages** in Next.js frontend for both tenant and manager user roles.

* Completed integration between Next.js frontend and NestJS backend via RTK Query, confirming stability of property creation and search workflows.

---

### Knowledge / Experience Gained:

* Learned how to integrate AWS SDK v3 in NestJS for file management on Amazon S3 (PutObjectCommand, DeleteObjectCommand).
* Mastered dynamic Prisma query building techniques when handling complex multi-condition search filters.
* Gained experience structuring Next.js App Router: separating Server Components for listing pages (SEO) and Client Components for interactive filter forms.
* Mastered frontend authentication state management and API response caching using Redux Toolkit and RTK Query.
