---
title: "Week 5 Worklog"
date: 2026-07-20
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Develop the core business features of the system (Property, Booking, Payment, Notification).
* Complete the API and handle complex business logic (booking state management, payment processing).
* Integrate the Frontend (Next.js) with the Backend API.
* Test features and fix bugs.
* Evaluate progress with mentor at the end of the week.

---

### Tasks carried out this week:

| Day | Task | Date | Reference |
|-----|------|------|-----------|
| Mon | - Develop core system features: <br>&emsp; + **Property module**: full CRUD, filter/search by price, area, location, property type <br>&emsp; + **Booking module**: schedule property viewings, update statuses (PENDING → CONFIRMED → CANCELLED) <br>&emsp; + **Contract module**: create rental contracts, store contract details <br>&emsp; + Validate input data with class-validator | 20/07/2026 | <https://docs.nestjs.com/> |
| Tue | - Finalize API and business logic: <br>&emsp; + Implement Notification module: create notifications when booking status changes <br>&emsp; + Implement Payment flow: integrate payment gateway (mock), update contract status <br>&emsp; + Implement pagination and sorting for property listing API <br>&emsp; + Write Swagger documentation for all API endpoints | 21/07/2026 | <https://docs.nestjs.com/openapi/introduction> |
| Wed | - Integrate Frontend with Backend: <br>&emsp; + Configure Next.js App Router, create main pages: Home, Property List, Property Detail, Booking <br>&emsp; + Call APIs from Frontend: axios/fetch with JWT interceptor <br>&emsp; + Implement global state management (Zustand or React Context) for auth state <br>&emsp; + Build components: PropertyCard, BookingForm, NavigationBar | 22/07/2026 | <https://nextjs.org/docs> |
| Thu | - Full system feature testing: <br>&emsp; + Test workflow: register → login → create listing → search → book a viewing <br>&emsp; + Verify role-based access: TENANT cannot create listings, LANDLORD cannot self-confirm bookings <br>&emsp; + Fix bugs found during testing <br>&emsp; + Test responsive design on mobile | 23/07/2026 | |
| Fri | - Overall progress review with mentor: <br>&emsp; + Demo completed features <br>&emsp; + Receive feedback on code quality and UX <br>&emsp; + Plan for Week 6: performance optimization | 24/07/2026 | |

---

### Week 5 Achievements:

* Completed all core business modules:
  * **Property**: full CRUD with filter/search, S3 image upload
  * **Booking**: lifecycle management with state machine (PENDING → CONFIRMED/CANCELLED)
  * **Contract**: rental contract creation and storage
  * **Notification**: automatic notifications when booking status changes

* Completed **Swagger documentation** for all API endpoints, enabling direct API testing via the UI.

* Successfully integrated **Next.js Frontend** with Backend API:
  * Main pages functional: Home, Property Listing (with filters), Property Detail, Booking Form
  * JWT interceptor automatically refreshes tokens when expired
  * Auth state managed globally via context

* Confirmed role-based authorization works correctly across the full business workflow.

* Received positive feedback from mentor, with a suggestion to optimize the number of database queries.

---

### Knowledge / Experience Gained:

* Gained a deep understanding of state machine patterns in booking management — clearly defining valid transitions is essential to prevent data inconsistencies.
* Learned how to implement standard pagination (cursor-based vs. offset-based): offset suits paginated UI, cursor suits infinite scroll.
* Understood Next.js App Router organization: Server Components vs. Client Components, and when to use `use client`.
* Practical experience: JWT interceptors need to handle race conditions when multiple requests simultaneously call the refresh token endpoint.
