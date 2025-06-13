# Sprint Documentation - Final Project IngeSoft V

## 🧩 Sprint 1: Setup and Foundation

### User Story 1: Configure Infrastructure with Terraform
**As** a DevOps engineer  
**I want** to set up the cloud infrastructure using Terraform  
**So that** I can deploy microservices in a repeatable and scalable manner.

**Acceptance Criteria:**
- Modular `.tf` file structure is implemented.
- Resources for `dev`, `stage`, and `prod` environments are provisioned.
- Remote backend for Terraform state is configured.
- Infrastructure diagram is documented.

---

### User Story 2: Implement GitFlow and Agile Board
**As** the development team  
**We want** to define and apply a GitFlow branching strategy and use an agile board  
**So that** we can organize our work and visually track progress.

**Acceptance Criteria:**
- Repository contains `main`, `develop`, `feature/*`, `release/*`, and `hotfix/*` branches.
- Board displays user stories and tasks in columns (To Do, In Progress, Done).
- Each story is assigned to a team member.

---

### User Story 3: Integrate Security and Quality Analysis
**As** a developer  
**I want** the pipeline to include static analysis (SonarQube) and image scanning (Trivy)  
**So that** I can detect quality and security issues automatically.

**Acceptance Criteria:**
- SonarQube is integrated and displays basic metrics (bugs, coverage, code smells).
- Trivy scans Docker images and reports vulnerabilities in the pipeline.
- Pipeline fails if critical vulnerabilities are found.

---

### User Story 4: Document Initial Design Patterns
**As** a software architect  
**I want** to identify and document existing architecture design patterns  
**So that** I can better understand and improve the system.

**Acceptance Criteria:**
- At least two existing patterns (e.g., Service Registry, API Gateway) are identified.
- For each pattern, purpose, diagram, and benefits are documented.

---

## 🚀 Sprint 2: Development and Continuous Delivery

### User Story 5: Create E2E Tests for Purchase Flow
**As** a QA engineer  
**I want** to implement end-to-end tests for the purchase process  
**So that** I can ensure all services interact correctly.

**Acceptance Criteria:**
- Tests simulate a user purchasing a product.
- Tests are executed in the CI pipeline.
- An automatic report is generated with results.

---

### User Story 6: Automate Deployment with CI/CD
**As** a developer  
**I want** a CI/CD pipeline that automatically deploys the services  
**So that** we can guarantee frequent and safe deliveries.

**Acceptance Criteria:**
- Pipeline builds, tests, and deploys automatically to `dev`.
- Deployment to `stage` and `prod` requires manual approval.
- Semantic versioning is applied.

---

### User Story 7: Implement Monitoring with Grafana and Prometheus
**As** a system operator  
**I want** to visualize metrics and alerts in dashboards  
**So that** I can monitor the microservices in real time.

**Acceptance Criteria:**
- Prometheus collects technical metrics from all microservices.
- Grafana displays dashboards with CPU, memory, traffic, etc.
- Alerts are configured for high resource usage.

---

### User Story 8: Configure Distributed Tracing
**As** a backend developer  
**I want** to implement distributed tracing using Zipkin or Jaeger  
**So that** I can trace requests across services.

**Acceptance Criteria:**
- Each service propagates the trace ID.
- Zipkin/Jaeger visualizes latency between services.
- Configuration is documented.