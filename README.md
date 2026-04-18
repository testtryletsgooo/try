# 🎓 Scholarship Distribution System

A comprehensive, microservice-based platform designed to streamline the scholarship application process. This system bridges the gap between students and organizations by offering role-based portals, integrated coding assessments, real-time messaging, and automated lifecycle management for scholarship programs.

---

## 🚀 Local Setup & Installation

The application utilizes a multi-container Docker architecture, separating the core API, real-time communications, and code evaluation into distinct microservices for easy deployment.

### Prerequisites
* [Docker](https://docs.docker.com/get-docker/) and Docker Compose installed on your machine.
* Git.

### 1. Clone the Repository
```bash
git clone [https://github.com/veervanshaj/scholarship-distribution-system.git](https://github.com/veervanshaj/scholarship-distribution-system.git)
cd scholarship-distribution-system
2. Configure Environment Variables
Create a .env file in the root directory of the backend (or configure them on your host server) with the following essential keys:

Code snippet
# Database configuration
DATABASE_URL="postgresql://user:password@host:port/dbname?schema=public"

# Authentication
JWT_SECRET="your_super_secret_jwt_key_here"
(Note: Redis URLs and internal routing are handled automatically by the docker-compose.yml network).

3. Build and Run the Containers
Spin up the entire microservice architecture using Docker Compose:

Bash
# Build the images and start the containers in detached mode
docker-compose up --build -d
4. Access the Services
Once running, the services are mapped to the following local ports:

Frontend UI: http://localhost:80 (Served via Nginx)

Main Backend API: http://localhost:5000

WebSocket Server: ws://localhost:4001

Coding Test Microservice: http://localhost:3000

Useful Docker Commands:

Bash
# View live logs for all running services
docker-compose logs -f

# View logs for a specific service (e.g., the coding test service)
docker-compose logs -f coding_service

# Stop and remove all containers, networks, and volumes
docker-compose down
🏗 System Architecture
Code snippet
graph TD
    Client([Web Client]) -->|HTTP :80| Frontend[Frontend: React + Vite + Nginx]
    Client -->|REST API :5000| Backend[Main Backend API]
    Client -->|WebSocket :4001| WS[WebSocket Server]
    
    Backend -->|Read/Write| DB[(PostgreSQL via Prisma)]
    Backend -.->|Job Queues / Cache| Redis[(Redis)]
    WS -.->|Pub/Sub| Redis
    
    Backend -->|Dispatch Code| Coding[Coding Test Microservice :3000]
    Coding -.->|Evaluation Results| Redis
🛠 Tech Stack & Features Overview
1. Frontend: React + Vite
Stack: React.js, Vite (for fast HMR and builds), Nginx (web server/reverse proxy).

Features: Provides the user interface for Applicants, Organizations, and Admins to interact with the system.

2. Main Backend API: Node.js + Express
Stack: Node.js, Express, PostgreSQL, Prisma ORM.

Features: * Role-Based Access Control (RBAC): Distinct environments and permissions for USER (Applicants), ORGANIZATION (Providers), and ADMIN roles.

Dynamic Applicant Profiles: Students can build comprehensive academic profiles detailing their CGPA, college, degree, and personal demographics.

Scholarship Lifecycle Management: Organizations can create and manage scholarships through specific states (UPCOMING, ACTIVE, CLOSED) while setting eligibility criteria, grant amounts, and minimum CGPA thresholds.

System Notifications: Global and targeted alerts (INFO, ALERT, SUCCESS) keep users updated on scholarship deadlines and application status changes.

3. Real-Time Server: Node.js + WebSockets
Stack: Node.js, WebSockets, Redis (Pub/Sub).

Features: * Applicant-Organization Chat: Runs on an isolated process (port 4001) to facilitate direct, real-time messaging between applicants and scholarship providers regarding application status or questions.

4. Coding Test Assessor: Isolated Node.js Microservice
Stack: Node.js, BullMQ, Redis.

Features: * Integrated Coding Assessments: Scholarships can toggle testMode on, linking the application to a specific coding challenge (testQuestionId).

Application Scoring: Safely executes and evaluates applicant code submissions, updating the applicant's testScore and totalScore within the main database.

Author: Veer Vanshaj Wadehra
