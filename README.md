# Hotel-Management-System
Software Requirements Specification (SRS)
Project: Hotel Management System (Full Stack)
Tech stack: Angular (frontend), Spring Boot (backend), MySQL/Postgres (DB), Docker, JWT/Auth, REST APIs
Duration planned: 1.5 months (6 weeks)
Version: 1.0
Author: Generated for the user
________________________________________
Table of Contents
1.	Introduction
2.	Overall Description
3.	Functional Requirements (features)
4.	Non-functional Requirements
5.	Data Model (entities & key attributes)
6.	REST API summary (important endpoints)
7.	UI / Pages & Components
8.	System Architecture & Tech Stack
9.	Security Considerations
10.	Testing & QA strategy
11.	Deployment & DevOps
12.	Project Plan — 6 weeks (step-by-step)
13.	Team & Roles (recommended)
14.	Risks & Mitigation
15.	Acceptance criteria and sign-off
16.	Appendix: sample JSON request/response
________________________________________
1. Introduction
1.1 Purpose
This SRS defines requirements for a Hotel Management System (HMS) built as a full stack web application using Angular for the frontend and Spring Boot for the backend. The goal is a production ready MVP in 6 weeks that supports core hotel operations: room inventory, reservations/booking, check-in/check-out, billing & payments, basic housekeeping assignments, user management and admin reporting.
1.2 Scope
The system will support:
•	Public booking (search availability, create reservation) for guests
•	Staff/admin panels (manage rooms, rates, bookings, check-ins, housekeeping, reports)
•	Secure user authentication/authorization for roles (Admin, Manager, Receptionist, Housekeeping, Guest)
•	Billing & payment recording (payment gateway integration optional — mock or test mode for MVP)
•	Notifications via email (reservation confirmations, invoices)
Out of scope for MVP (can be added later): channel management (OTAs), dynamic pricing/AI, advanced housekeeping optimization, loyalty program.
1.3 Definitions & Acronyms
•	HMS — Hotel Management System
•	MVP — Minimum Viable Product
•	API — Application Programming Interface
•	JWT — JSON Web Token
•	DB — Database
________________________________________
2. Overall Description
2.1 Product perspective
This is a web-based SaaS/installed application. Frontend (Angular) calls backend REST APIs (Spring Boot). Backend handles business logic, persistence, authentication, and integration with optional third-party services (email, payment gateway).
2.2 User classes and characteristics
•	Guest / Customer: Searches rooms, books, views reservation, cancels reservation.
•	Receptionist: Creates bookings, performs check-in/check-out, prints invoices.
•	Manager/Admin: Manages room inventory, rates, users, views reports, config settings.
•	Housekeeping staff: Views assigned rooms/tasks, marks status (clean/dirty).
2.3 Operating environment
•	Frontend: modern browsers (Chrome, Edge, Firefox)
•	Backend: Linux server (Ubuntu) or containerized (Docker) with Java 17+ runtime
•	Database: MySQL 8.x / PostgreSQL 14+
2.4 Design & Implementation constraints
•	Use RESTful API design
•	JWT-based stateless authentication
•	Responsive UI for desktop and tablet (mobile basic)
________________________________________
3. Functional Requirements (high-level)
Each feature has ID, description, actors and acceptance criteria.
FR-1: Authentication & Authorization
•	Description: Users can register (guest) or be created by admin. Login returns JWT token. Role-based access control.
•	Actors: Guest, Receptionist, Manager, Admin
•	Acceptance: Protected endpoints reject unauthorized calls. Admin can create roles.
FR-2: Room & Room Type Management
•	Description: Admin can create room types (Single, Double, Suite) and rooms (room number, floor, amenities, status).
•	Actors: Admin/Manager
•	Acceptance: Rooms list shows filters; Admin can change room status (available/occupied/maintenance).
FR-3: Search & Availability
•	Description: Users can search rooms by date range, occupancy, room type.
•	Actors: Guest, Receptionist
•	Acceptance: Results show available rooms with nightly rate and cancellation rules.
FR-4: Booking / Reservation
•	Description: Create reservation specifying guest details, room(s), date range, special requests. Generate reservation ID.
•	Actors: Guest, Receptionist
•	Acceptance: Booking stored, email confirmation sent (or simulated) with reservation details.
FR-5: Check-in / Check-out
•	Description: Receptionist checks in guest (assigns room key, marks room occupied), and checks out (calculates final bill).
•	Actors: Receptionist
•	Acceptance: Room status updated; invoice generated on checkout.
FR-6: Billing & Payments
•	Description: Calculate charge (room rate * nights + taxes + services). Record payments (cash/card). Integrate gateway later.
•	Actors: Receptionist, Guest
•	Acceptance: Invoice PDF generation (or printable view); payments recorded and linked to reservation.
FR-7: Housekeeping Management
•	Description: Create housekeeping tasks per room, assign to staff, update status (clean/dirty/in-progress).
•	Actors: Manager, Housekeeping
•	Acceptance: Housekeeping staff see assigned tasks and update status.
FR-8: Reports & Analytics
•	Description: Basic reports: occupancy rate, revenue per period, reservation list, overdue checkouts.
•	Actors: Manager
•	Acceptance: Exportable CSV for each report.
FR-9: Notifications
•	Description: Email notifications on booking create/modify/cancel and payment receipts.
•	Actors: System
•	Acceptance: Emails succeed in test environment (SMTP or mock).
FR-10: Audit & Logging
•	Description: Record important actions (login/logout, booking created/modified, payments) for traceability.
•	Actors: System, Admin
•	Acceptance: Admin can view audit log entries.
________________________________________
4. Non functional Requirements
•	NFR-1 Performance: System must handle 100 concurrent users for MVP; API responses < 500ms for normal queries.
•	NFR-2 Security: Follow OWASP best practices; store passwords with bcrypt; use HTTPS in production; JWT tokens expire after configurable time.
•	NFR-3 Availability: 99.5% availability for deployed service (dependent on hosting choice).
•	NFR-4 Scalability: Stateless backend so it can scale horizontally behind a load balancer.
•	NFR-5 Maintainability: Code should have unit tests and follow clean architecture with separation of concerns.
•	NFR-6 Backup & Recovery: Nightly DB backups and retention policy (7–30 days).
•	NFR-7 Usability: UI must be responsive and intuitive; basic accessibility (keyboard nav, semantic HTML).
________________________________________
5. Data Model (core entities)
(Attributes are illustrative — finalize during DB design)
•	User: id, name, email, passwordHash, role, phone, createdAt
•	Role: id, name (ADMIN, MANAGER, RECEPTION, HOUSEKEEPING, GUEST)
•	RoomType: id, name, description, maxOccupancy, baseRate
•	Room: id, number, floor, roomTypeId, status (AVAILABLE/OCCUPIED/MAINTENANCE), features
•	Reservation: id, reservationCode, guestId, roomId(s), startDate, endDate, status (BOOKED/CHECKED_IN/CHECKED_OUT/CANCELLED), totalAmount
•	Payment: id, reservationId, amount, method (CASH/CARD/ONLINE), status, transactionRef, date
•	Invoice: id, reservationId, issuedAt, items (room charges, services), total
•	HousekeepingTask: id, roomId, assignedTo(userId), status, notes, scheduledAt
•	AuditLog: id, userId, action, details, timestamp
Include indexes on email, reservationCode, room number.
________________________________________
6. REST API summary (examples)
Auth
•	POST /api/auth/login — {email, password} → {token, user}
•	POST /api/auth/register — {name,email,password,phone} → {user}
Rooms
•	GET /api/rooms?from=YYYY-MM-DD&to=YYYY-MM-DD&type=... — list & availability
•	POST /api/rooms — create room (admin)
•	PUT /api/rooms/{id} — update
Reservations
•	POST /api/reservations — create reservation (guest or receptionist)
•	GET /api/reservations/{id} — get reservation
•	PUT /api/reservations/{id}/cancel — cancel
Payments
•	POST /api/reservations/{id}/payments — record payment
Housekeeping
•	GET /api/housekeeping/tasks?assignee=... — tasks for a user
•	PUT /api/housekeeping/tasks/{id} — update status
Admin / Reports
•	GET /api/reports/occupancy?from=&to= — returns occupancy summary
All endpoints require Authorization: Bearer <token> except public search and register (if allowed).
Include Swagger/OpenAPI documentation accessible at /swagger-ui.html in backend.
________________________________________
7. UI / Pages & Components
Public / Guest:
•	Landing / Home
•	Room search & availability
•	Room details
•	Reservation checkout
•	My reservations (view/cancel)
Auth / Common:
•	Login / Register / Forgot password
Receptionist dashboard:
•	Quick search reservations
•	Create reservation
•	Check-in / Check-out
•	Payments & invoices
Manager/Admin dashboard:
•	Rooms & room-types management
•	Rate management
•	Reports & analytics
•	User & role management
Housekeeping app (simple UI):
•	Task list
•	Update status
Components: header, nav, filters, booking calendar, reservation form, invoice modal/pdf, data tables with sorting/pagination, notifications/toasts.
________________________________________
8. System Architecture & Tech Stack
Frontend:
•	Angular 14+ (CLI)
•	RxJS for reactive streams
•	Angular Material or TailwindCSS (pick one)
•	State management: NgRx optional (for MVP simple services + component state is OK)
Backend:
•	Spring Boot 3.x (Java 17+)
•	Spring Security (JWT), Spring Data JPA, Hibernate
•	Database: MySQL or PostgreSQL
•	Lombok for boilerplate reduction
•	OpenAPI (springdoc) for API docs
DevOps / Tools:
•	GitHub (repo) + GitHub Actions for CI
•	Docker + docker-compose for local development (app + db)
•	Postman collection
•	CI pipeline: run backend unit tests, build docker images, push to registry
•	Deployment target: VPS / AWS ECS or Elastic Beanstalk / Heroku for MVP
3rd party Integrations:
•	SMTP provider (SendGrid/Mailtrap for testing)
•	Payment gateway sandbox (Stripe/PayPal) — optional for MVP
________________________________________
9. Security Considerations
•	Use HTTPS in production; redirect HTTP → HTTPS
•	Passwords hashed with bcrypt (PBKDF2/Argon2 recommended)
•	Input validation and sanitization to prevent XSS/SQLi
•	Rate-limiting on auth endpoints
•	Secure JWT with short expiry + refresh tokens if needed
•	PCI requirements if accepting card payments (recommend using gateway tokenization)
________________________________________
10. Testing & QA Strategy
•	Unit tests for backend services (JUnit + Mockito)
•	Integration tests for controllers (Spring Boot test slice)
•	Frontend unit tests
1️⃣ Deployment & DevOps
This part explains how the application will be built, tested, and deployed, and how the environment will be maintained.
Key elements:
•	Environments:
o	Local dev: via docker-compose (frontend + backend + DB).
o	Staging: deployed on a small VPS or cloud (AWS/Heroku/Azure) for testing.
o	Production: scalable hosting (AWS ECS, Elastic Beanstalk, or Kubernetes).
•	CI/CD: GitHub Actions pipeline → run tests, build Angular and Spring Boot artifacts, create Docker images, push to registry, deploy to staging/prod.
•	Configuration management: use environment variables or .env files for secrets (DB password, JWT secret, SMTP keys).
•	Logging & monitoring: centralized logs (stdout → ELK or CloudWatch), uptime monitoring, alerts for API errors.
•	Database management: versioned migrations (Liquibase/Flyway), nightly backups (dump to S3/storage).
•	Security: HTTPS/TLS certs, least-privilege access for servers and DB.
________________________________________
2️⃣ Project Plan — 6 weeks (step-by-step)
A week-by-week roadmap to deliver the MVP in 1.5 months:
Week	Focus	Deliverables
0 (Pre-work)	Repo, dev env, Docker skeleton	GitHub repo, initial docker-compose
1	SRS approval, wireframes, app skeleton	Signed-off SRS, wireframes, Angular & Spring Boot scaffolding
2	Data model, Auth, Room mgmt	DB schema, JWT login/register, room CRUD
3	Search & Booking	Availability API, booking flow, email mock
4	Check-in/out, Billing, Housekeeping	Endpoints + UIs for these modules
5	Reports & Notifications, polish	Occupancy/revenue reports, email, unit tests
6	Testing, Deployment, Handoff	E2E tests, staging deploy, docs, demo
(If solo, extend timeline to ~8–10 weeks.)
________________________________________
3️⃣ Team & Roles (recommended)
Even a small MVP benefits from clear roles:
Role	Responsibilities
Product Owner (you/manager)	Finalize requirements, prioritize features, sign-off deliverables
Backend Dev (Spring Boot)	API design, DB schema, business logic, security, unit tests
Frontend Dev (Angular)	UI design, component coding, API integration, styling, responsiveness
QA/Test Engineer	Write Cypress/E2E tests, verify user stories, run regressions
DevOps (part-time)	Set up CI/CD, monitoring, deploy to staging/prod, DB backups
In a solo project, you’ll wear multiple hats — plan time for each role.
________________________________________
4️⃣ Risks & Mitigation
Helps anticipate blockers before they occur:
Risk	Probability	Impact	Mitigation
Scope creep	Medium	High	Lock MVP features; defer extras
Payment integration complexity	Medium	Medium	Use sandbox/test mode or mock gateway
Email delivery failures	Medium	Low	Use Mailtrap/SendGrid test env; log unsent mails
Data loss	Low	High	Automate nightly DB backups
Security breach	Low	High	Follow OWASP, enforce HTTPS, sanitize inputs
Performance issues	Low	Medium	Cache room availability results; run load tests
________________________________________
5️⃣ Acceptance Criteria & Sign-off
Defines when the system is “done”:
•	All major flows (search → booking → payment → check-in → check-out → invoice) work end-to-end.
•	Role-based access control enforced.
•	Occupancy & revenue reports exportable (CSV/PDF).
•	Booking confirmations & receipts emailed (or logged in test mode).
•	System deployed to staging with HTTPS and basic monitoring.
•	API response time for search < 500 ms (with ≤100 concurrent users).
•	Unit test coverage ≥ 60% (backend core modules).
Sign-off: Product Owner (or client) verifies all above are satisfied.
________________________________________
6️⃣ Appendix: Sample JSON Request/Response
Shows how the API behaves for common operations.
Example: Create Reservation
Request (POST /api/reservations):
{
  "guest": {
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+919999999999"
  },
  "roomTypeId": 2,
  "startDate": "2025-10-01",
  "endDate": "2025-10-05",
  "payment": { "method": "CASH" },
  "specialRequests": "Late arrival"
}
Response:
{
  "reservationId": 1234,
  "reservationCode": "HMS-20251001-ABCD",
  "status": "BOOKED",
  "totalAmount": 20000
}
Example: Error (validation failure)
{
  "timestamp": "2025-09-18T12:45:00Z",
  "status": 400,
  "error": "Bad Request",
  "message": "End date must be after start date",
  "path": "/api/reservations"
}

