# SomNOG 9 Event Management System — Microservices Practical

## Overview

This repository contains the practical microservices development exercise for the **SomNOG 9 Event Management System**.

The system is designed as independent microservices. Each group owns and manages its **own PostgreSQL database**. Services must communicate with each other through **REST APIs** and must not directly access another service's database.

## Architecture

```text
                         Next.js Frontend
                                |
                                v
                         API Gateway
                           Group 6
                                |
        +-----------------------+-----------------------+
        |          |            |          |            |
        v          v            v          v            v
     Group 1    Group 2      Group 3    Group 4      Group 5
     Workshop   Truck        Facilitator Training    Feedback
     Service    Service      Service     Service     Service
        |          |            |          |            |
        v          v            v          v            v
       DB #1      DB #2        DB #3      DB #4       DB #5
```

### Core Rule

> **One Microservice = One Independent Database**

A service owns its database and its data. Other services must not connect directly to that database.

Cross-service information is exchanged using IDs through REST API requests.

---

# Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14 (App Router) |
| Backend | NestJS + TypeScript |
| Database | PostgreSQL 15 |
| ORM | TypeORM |
| Authentication | JWT + Bcrypt |
| Validation | class-validator |
| API Documentation | Swagger / OpenAPI |
| Containerization | Docker + docker-compose |
| Version Control | Git + GitHub |
| API Client | Axios |
| Styling | Tailwind CSS |

----

# Microservices

| Group | Service | Database | Port |
|---|---|---|---:|
| Group 1 | Workshop & Application | `somnog_workshop_db` | 3001 |
| Group 2 | Truck Management | `somnog_truck_db` | 3002 |
| Group 3 | Facilitator & Content | `somnog_facilitator_db` | 3003 |
| Group 4 | Training & Assessment | `somnog_training_db` | 3004 |
| Group 5 | Feedback, Certificate & Report | `somnog_feedback_db` | 3005 |
| Group 6 | Integration & Platform | `somnog_platform_db` | 3006 |

---

# Group 1 — Workshop & Application Service

**Database:** `somnog_workshop_db`

## Responsibilities

- Create and manage workshops
- Register participants
- Manage participant accounts
- Submit truck applications
- Approve or reject applications
- Create enrollment records
- Expose APIs for other services

## API Endpoints

```text
POST   /workshops
GET    /workshops
GET    /workshops/:id
PATCH  /workshops/:id/status

POST   /participants/register
POST   /participants/login
GET    /participants/:id

POST   /applications
GET    /applications
PATCH  /applications/:id/approve
PATCH  /applications/:id/reject

GET    /enrollments/workshop/:id
```

## Sample Workshop JSON

```json
{
  "id": "a1000001-0000-0000-0000-000000000009",
  "name": "SomNOG Workshop 9",
  "year": 2026,
  "workshop_no": 9,
  "location": "Mogadishu",
  "start_date": "2026-09-20",
  "end_date": "2026-09-22",
  "max_capacity": 30,
  "status": "Open"
}
```

## Sample Participant JSON

```json
{
  "id": "p1000001-0000-0000-0000-000000000001",
  "first_name": "Ahmed",
  "last_name": "Mohamed",
  "email": "ahmed.mohamed@example.com",
  "phone": "+252610000001",
  "institution": "University of Mogadishu",
  "job_title": "Network Engineer",
  "status": "Active"
}
```

## Application Request

```json
{
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "truck_id": "t1000001-0000-0000-0000-000000000001",
  "motivation": "I want to improve my networking skills."
}
```

---

# Group 2 — Truck Management Service

**Database:** `somnog_truck_db`

## Responsibilities

- Manage training trucks
- Manage truck facilities
- Manage equipment
- Manage training contents
- Manage truck availability per workshop

## API Endpoints

```text
GET    /trucks
GET    /trucks/:id
GET    /trucks/:id/facilities
GET    /trucks/:id/equipment
GET    /trucks/:id/contents
GET    /trucks/availability/:workshop_id

POST   /trucks
POST   /trucks/:id/facilities
POST   /trucks/:id/equipment
POST   /trucks/:id/contents

PATCH  /trucks/availability
```

## Sample Trucks JSON

```json
[
  {
    "id": "t1000001-0000-0000-0000-000000000001",
    "name": "Networking Truck",
    "type": "Networking",
    "description": "Practical networking laboratory",
    "status": "Active"
  },
  {
    "id": "t1000001-0000-0000-0000-000000000002",
    "name": "Cybersecurity Truck",
    "type": "Cybersecurity",
    "description": "Cybersecurity practical laboratory",
    "status": "Active"
  },
  {
    "id": "t1000001-0000-0000-0000-000000000003",
    "name": "Software Development Truck",
    "type": "Software Development",
    "description": "Software development laboratory",
    "status": "Active"
  }
]
```

## Equipment JSON

```json
{
  "id": "eq-001",
  "truck_id": "t1000001-0000-0000-0000-000000000001",
  "equipment_name": "Cisco Router",
  "quantity": 10,
  "description": "Enterprise networking router"
}
```

## Availability Request

```json
{
  "truck_id": "t1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "status": "Available"
}
```

> `workshop_id` is owned by Group 1. Group 2 stores it only as a cross-service reference.

---

# Group 3 — Facilitator & Content Service

**Database:** `somnog_facilitator_db`

## Responsibilities

- Register facilitators
- Manage facilitator profiles
- Manage expertise
- Create training topics
- Manage learning materials
- Provide facilitator and content APIs

## API Endpoints

```text
GET    /facilitators
GET    /facilitators/:id
POST   /facilitators
PATCH  /facilitators/:id

POST   /facilitators/:id/expertise
GET    /facilitators/:id/expertise

GET    /topics
GET    /topics/:id
POST   /topics

POST   /topics/:id/materials
GET    /topics/:id/materials
```

## Facilitator JSON

```json
[
  {
    "id": "f1000001-0000-0000-0000-000000000001",
    "name": "Ahmed Hassan",
    "email": "ahmed.h@somnog.org",
    "bio": "Network engineer and trainer",
    "photo_url": "/images/ahmed.jpg",
    "status": "Active"
  },
  {
    "id": "f1000001-0000-0000-0000-000000000002",
    "name": "Mohamed Ali",
    "email": "mohamed.a@somnog.org",
    "bio": "Cybersecurity specialist",
    "photo_url": "/images/mohamed.jpg",
    "status": "Active"
  },
  {
    "id": "f1000001-0000-0000-0000-000000000003",
    "name": "Fatima Omar",
    "email": "fatima.o@somnog.org",
    "bio": "Software development trainer",
    "photo_url": "/images/fatima.jpg",
    "status": "Active"
  }
]
```

## Expertise JSON

```json
{
  "id": "exp-001",
  "facilitator_id": "f1000001-0000-0000-0000-000000000001",
  "expertise": "Networking",
  "level": "Expert"
}
```

## Training Topic JSON

```json
{
  "id": "topic-001",
  "title": "Network Fundamentals",
  "description": "Introduction to networking concepts",
  "truck_type": "Networking",
  "day": "Day 1"
}
```

## Learning Material JSON

```json
{
  "id": "material-001",
  "topic_id": "topic-001",
  "title": "Network Fundamentals Slides",
  "type": "Slides",
  "url": "https://example.com/network-fundamentals.pdf"
}
```

---

# Group 4 — Training & Assessment Service

**Database:** `somnog_training_db`

## Responsibilities

- Create training sessions
- Assign facilitator and topic to sessions
- Record attendance
- Create assessments
- Record assessment scores
- Calculate final results
- Expose result APIs

## API Endpoints

```text
POST   /sessions
GET    /sessions/workshop/:id
GET    /sessions/:id

POST   /attendance
GET    /attendance/session/:id
GET    /attendance/participant/:id

POST   /assessments
POST   /assessments/:id/results
GET    /assessments/:id/results

GET    /results/workshop/:id
GET    /results/participant/:id
```

## Session JSON

```json
{
  "id": "session-001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "truck_id": "t1000001-0000-0000-0000-000000000001",
  "facilitator_id": "f1000001-0000-0000-0000-000000000001",
  "topic_id": "topic-001",
  "day": "Day 1",
  "session_date": "2026-09-20"
}
```

## Attendance JSON

```json
{
  "session_id": "session-001",
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "status": "Present"
}
```

## Assessment JSON

```json
{
  "id": "assessment-001",
  "session_id": "session-001",
  "title": "Networking Assessment",
  "total_marks": 100
}
```

## Assessment Result JSON

```json
{
  "assessment_id": "assessment-001",
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "score": 85
}
```

## Final Result JSON

```json
{
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "score": 85,
  "result": "PASS"
}
```

### Result Rule

```text
Score >= 50  → PASS
Score < 50   → FAIL
```

---

# Group 5 — Feedback, Certificate & Report Service

**Database:** `somnog_feedback_db`

## Responsibilities

- Collect participant feedback
- Generate certificates for participants who pass
- Send result notifications
- Generate workshop reports
- Provide statistics APIs

## API Endpoints

```text
POST   /feedback
GET    /feedback/workshop/:id
GET    /feedback/truck/:id

POST   /certificates/generate
GET    /certificates/participant/:id
GET    /certificates/:id/download

POST   /notifications/send
GET    /notifications/participant/:id

POST   /reports/workshop/:id
GET    /reports/workshop/:id
GET    /reports/statistics/:workshop_id
```

## Feedback JSON

```json
{
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "truck_id": "t1000001-0000-0000-0000-000000000001",
  "facilitator_id": "f1000001-0000-0000-0000-000000000001",
  "rating": 5,
  "comment": "The training was very useful."
}
```

## Certificate JSON

```json
{
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "certificate_no": "SNOG9-001",
  "status": "Issued",
  "file_url": "/certificates/SNOG9-001.pdf"
}
```

## Notification JSON

```json
{
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "type": "Result",
  "message": "Congratulations, you passed the SomNOG 9 training.",
  "channel": "Email",
  "status": "Sent"
}
```

---

# Group 6 — Integration & Platform Service

**Database:** `somnog_platform_db`

## Responsibilities

- API Gateway
- Authentication
- Authorization
- JWT
- User roles
- Service-to-service communication
- Swagger/OpenAPI
- Next.js frontend
- Docker
- Logging and monitoring
- Integration testing

## API Gateway Routes

```text
/api/workshops/*      → Workshop Service       :3001
/api/trucks/*         → Truck Service           :3002
/api/facilitators/*   → Facilitator Service     :3003
/api/training/*       → Training Service        :3004
/api/feedback/*       → Feedback Service        :3005
/api/auth/*           → Platform Service        :3006
```

---

# Cross-Service ID Ownership

The following IDs are owned by specific services.

| ID | Owner | Used By |
|---|---|---|
| `workshop_id` | Group 1 | Groups 2, 3, 4, 5 |
| `participant_id` | Group 1 | Groups 4, 5 |
| `truck_id` | Group 2 | Groups 1, 3, 4, 5 |
| `facilitator_id` | Group 3 | Group 4 |
| `topic_id` | Group 3 | Groups 3, 4 |
| `session_id` | Group 4 | Group 4 |
| `assessment_id` | Group 4 | Groups 4, 5 |

## Important

If Group 4 needs a participant:

```text
DO NOT:
Connect Group 4 directly to somnog_workshop_db

DO:
Call Group 1 API
```

Example:

```http
GET /api/workshops/participants/p1000001-0000-0000-0000-000000000001
```

---

# Practical Exercise

## Exercise 1 — Create Your NestJS Service

Each group must create an independent NestJS application.

Example:

```text
group1-workshop-service
group2-truck-service
group3-facilitator-service
group4-training-service
group5-feedback-service
group6-platform-service
```

Each service should have:

```text
src/
├── modules/
├── controllers/
├── services/
├── entities/
├── dto/
└── main.ts
```

The exact internal structure can be adjusted by each group, but the service must remain independently deployable.

---

# Exercise 2 — Create Independent PostgreSQL Database

Each group creates its own database.

Example:

```sql
CREATE DATABASE somnog_workshop_db;
CREATE DATABASE somnog_truck_db;
CREATE DATABASE somnog_facilitator_db;
CREATE DATABASE somnog_training_db;
CREATE DATABASE somnog_feedback_db;
CREATE DATABASE somnog_platform_db;
```

### Database Rule

Never do this:

```text
Group 4
   |
   +----> Group 1 PostgreSQL Database
```

Instead:

```text
Group 4
   |
   +----> Group 4 PostgreSQL Database
   |
   +----> REST API ---> Group 1
```

---

# Exercise 3 — Implement CRUD

Each group should implement CRUD operations for its own resources.

```text
CREATE
READ
UPDATE
DELETE
```

Example:

```http
POST   /trucks
GET    /trucks
GET    /trucks/:id
PATCH  /trucks/:id
DELETE /trucks/:id
```

---

# Exercise 4 — DTO Validation

Use NestJS DTOs and `class-validator`.

Example:

```typescript
import { IsEmail, IsNotEmpty, IsString } from 'class-validator';

export class CreateParticipantDto {
  @IsNotEmpty()
  @IsString()
  first_name: string;

  @IsNotEmpty()
  @IsString()
  last_name: string;

  @IsEmail()
  email: string;
}
```

Students should validate:

- Required fields
- Email format
- Numeric fields
- Enum/status values
- UUID values
- Maximum/minimum values

---

# Exercise 5 — Swagger API Documentation

Every service must expose Swagger documentation.

Example:

```text
http://localhost:3001/api
http://localhost:3002/api
http://localhost:3003/api
http://localhost:3004/api
http://localhost:3005/api
http://localhost:3006/api
```

Students should document:

- Endpoint
- HTTP method
- Request body
- Parameters
- Response
- Error responses

---

# Exercise 6 — Cross-Service API Communication

### Scenario

A participant applies for a truck.

Group 1 receives:

```json
{
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "truck_id": "t1000001-0000-0000-0000-000000000001"
}
```

Group 1 can validate the truck by calling Group 2:

```http
GET /api/trucks/t1000001-0000-0000-0000-000000000001
```

Group 1 does not query the Truck database directly.

---

# Exercise 7 — Complete Integration Flow

Students should demonstrate the following complete workflow:

```text
1. Create Workshop
       |
       v
2. Create Trucks
       |
       v
3. Make Trucks Available
       |
       v
4. Create Facilitators
       |
       v
5. Create Training Topics
       |
       v
6. Register Participant
       |
       v
7. Submit Application
       |
       v
8. Approve Application
       |
       v
9. Create Training Session
       |
       v
10. Record Attendance
       |
       v
11. Create Assessment
       |
       v
12. Submit Score
       |
       v
13. Calculate PASS / FAIL
       |
       v
14. Generate Certificate
       |
       v
15. Send Notification
       |
       v
16. Generate Report
```

---

# Example Cross-Service Flow

## Step 1 — Workshop Service

```http
POST /workshops
```

Response:

```json
{
  "id": "a1000001-0000-0000-0000-000000000009",
  "name": "SomNOG Workshop 9",
  "status": "Open"
}
```

## Step 2 — Truck Service

Use the workshop ID:

```http
PATCH /trucks/availability
```

```json
{
  "truck_id": "t1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "status": "Available"
}
```

## Step 3 — Participant Applies

```http
POST /applications
```

```json
{
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "truck_id": "t1000001-0000-0000-0000-000000000001"
}
```

## Step 4 — Training Service

Create a session using IDs from other services:

```json
{
  "workshop_id": "a1000001-0000-0000-0000-000000000009",
  "truck_id": "t1000001-0000-0000-0000-000000000001",
  "facilitator_id": "f1000001-0000-0000-0000-000000000001",
  "topic_id": "topic-001",
  "day": "Day 1",
  "session_date": "2026-09-20"
}
```

## Step 5 — Assessment Result

```json
{
  "assessment_id": "assessment-001",
  "participant_id": "p1000001-0000-0000-0000-000000000001",
  "score": 85
}
```

## Step 6 — Certificate Service

Group 5 requests the result from Group 4:

```http
GET /api/training/results/participant/p1000001-0000-0000-0000-000000000001
```

If the result is:

```json
{
  "score": 85,
  "result": "PASS"
}
```

Group 5 can generate the certificate.

---

# Testing with Postman

Students should create a Postman collection:

```text
SomNOG 9 Microservices
│
├── Group 1 - Workshop
│   ├── Create Workshop
│   ├── List Workshops
│   ├── Register Participant
│   └── Create Application
│
├── Group 2 - Trucks
│   ├── List Trucks
│   ├── Truck Details
│   ├── Add Equipment
│   └── Set Availability
│
├── Group 3 - Facilitators
│   ├── List Facilitators
│   ├── Create Facilitator
│   ├── Add Expertise
│   └── Create Topic
│
├── Group 4 - Training
│   ├── Create Session
│   ├── Mark Attendance
│   ├── Create Assessment
│   └── Submit Result
│
├── Group 5 - Feedback
│   ├── Submit Feedback
│   ├── Generate Certificate
│   ├── Send Notification
│   └── Generate Report
│
└── Group 6 - Gateway
    ├── Login
    ├── Gateway Workshop
    ├── Gateway Truck
    ├── Gateway Training
    └── Gateway Feedback
```

---

# GitHub Branching Strategy

```text
main
  |
  └── develop
        |
        ├── group1/workshop-service
        ├── group2/truck-service
        ├── group3/facilitator-service
        ├── group4/training-service
        ├── group5/feedback-service
        └── group6/platform
```

## Branch Rules

### `main`

Production/protected branch.

### `develop`

Integration branch.

### Group branches

Each group develops its own service independently.

Example:

```bash
git checkout -b group2/truck-service
```

Commit:

```bash
git add .
git commit -m "feat: implement truck management API"
git push origin group2/truck-service
```

---

# Student Deliverables

Each group must submit:

- [ ] NestJS microservice
- [ ] Independent PostgreSQL database
- [ ] Database entities
- [ ] Database migrations
- [ ] DTOs
- [ ] Controllers
- [ ] Services
- [ ] REST APIs
- [ ] Validation
- [ ] Swagger documentation
- [ ] Seed/sample JSON data
- [ ] Postman collection
- [ ] README for the service
- [ ] Git branch
- [ ] API integration with required services

---

# Microservice Rules

## Rule 1 — Database Independence

Each service has its own database.

```text
Workshop Service → somnog_workshop_db
Truck Service → somnog_truck_db
Facilitator Service → somnog_facilitator_db
Training Service → somnog_training_db
Feedback Service → somnog_feedback_db
Platform Service → somnog_platform_db
```

## Rule 2 — No Shared Database

Do not create:

```text
One PostgreSQL Database
        |
        +-- Group 1
        +-- Group 2
        +-- Group 3
        +-- Group 4
        +-- Group 5
        +-- Group 6
```

Instead:

```text
Group 1 → DB 1
Group 2 → DB 2
Group 3 → DB 3
Group 4 → DB 4
Group 5 → DB 5
Group 6 → DB 6
```

## Rule 3 — API Communication

Communication between services must happen through REST APIs.

```text
Service A
    |
    | HTTP/REST
    v
Service B
    |
    v
Service B Database
```

## Rule 4 — ID Ownership

Only the service that owns an entity creates and manages that entity's ID.

Example:

```text
participant_id → Group 1
truck_id       → Group 2
facilitator_id → Group 3
topic_id       → Group 3
session_id     → Group 4
assessment_id  → Group 4
```

---

# Recommended Docker Structure

Each service should eventually run independently:

```text
somnog/
├── group1-workshop-service/
├── group2-truck-service/
├── group3-facilitator-service/
├── group4-training-service/
├── group5-feedback-service/
├── group6-platform-service/
└── docker-compose.yml
```

Example containers:

```text
workshop-service      → 3001
workshop-db           → PostgreSQL

truck-service         → 3002
truck-db              → PostgreSQL

facilitator-service   → 3003
facilitator-db        → PostgreSQL

training-service      → 3004
training-db           → PostgreSQL

feedback-service      → 3005
feedback-db           → PostgreSQL

platform-service      → 3006
platform-db           → PostgreSQL
```

---

# Learning Objectives

After completing this practical exercise, students should be able to:

1. Build a NestJS microservice.
2. Create RESTful APIs.
3. Connect a microservice to PostgreSQL.
4. Design an independent database.
5. Implement CRUD operations.
6. Use DTO validation.
7. Document APIs with Swagger.
8. Exchange data between microservices.
9. Use cross-service IDs correctly.
10. Understand database-per-service architecture.
11. Use Git branches for team development.
12. Integrate multiple microservices through an API Gateway.
13. Test APIs with Postman.
14. Containerize services using Docker.

---

# Final Practical Demonstration

At the final presentation, each group should demonstrate:

```text
                    API Gateway
                         |
       +---------+-------+-------+---------+
       |         |       |       |         |
       v         v       v       v         v
    Group 1   Group 2 Group 3 Group 4   Group 5
       |         |       |       |         |
       v         v       v       v         v
      DB1       DB2     DB3     DB4       DB5
```

The complete system should demonstrate:

**Workshop → Truck → Application → Training → Assessment → Result → Feedback → Certificate → Report**

while maintaining **independent databases for every microservice**.

---

# Source

Based on the **SomNOG 9 Event Management System — Group Assignment & Service Development Document**.

Technology stack, service responsibilities, API endpoints, database ownership, cross-service IDs, and integration structure in this README are based on that assignment document.
