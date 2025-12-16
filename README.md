# Coding Challenge: Distributed Order Processing System

## 🎯 Objective
Design and implement a distributed system in **Java 21** that processes e-commerce orders asynchronously.

**Role Level:** Mid-Level / Senior
**Focus:** We are looking for modern Java practices (**Virtual Threads**), solid engineering fundamentals (idempotency, resilience), clear documentation (**OpenAPI**), and production-ready containerization.

## ⏳ Time Limit
You have **2 weeks** to complete this challenge.

## 🛠 Technical Requirements

### 1. The Stack
* **Java 21** (Strict Requirement).
* **Spring Boot 3.2+** (Or any framework supporting Virtual Threads).
* **RabbitMQ** (Messaging).
* **PostgreSQL** or **MySQL** (Persistence).
* **Docker & Docker Compose** (Containerization).
* **OpenAPI / Swagger** (Documentation).

### 2. System Architecture



#### Service A: Order API (Producer)
* **Endpoint:** `POST /api/v1/orders`
* **Payload:** `{ "id": "uuid", "item": "String", "price": 100.50 }`
* **Concurrency:** **Must use Java 21 Virtual Threads** to handle HTTP requests.
    * *Hint: In Spring Boot 3.2+, this is often a single configuration line.*
* **Documentation:** Expose an **OpenAPI 3.0 / Swagger UI** page (e.g., at `/swagger-ui.html`) that documents the endpoint and example payloads.
* **Logic:**
    * Validate the payload (price > 0).
    * Generate a **`correlationId`** (UUID) for tracing.
    * Send the order + `correlationId` to RabbitMQ.

#### Service B: Order Worker (Consumer)
* **Logic:**
    * Listen to the queue.
    * Process the order (simulate a delay of 2 seconds).
    * **Idempotency (CRITICAL):** Ensure that if a message is redelivered, the order is **not** processed/saved twice.
    * Save the order to the Database with status `PROCESSED`.
* **Simulated Failure:** If the order `id` contains the text `"FAIL"`, the worker must throw an exception.
* **Resilience:**
    * The worker **must not crash** on exceptions.
    * Failed messages must be logged with the `correlationId`.

### 3. Containerization Requirements
You must containerize both Java applications.
* Create a `Dockerfile` for `order-api` and `order-worker`.
* **Requirement:** Use **Multi-Stage Builds** to separate the build environment (Maven/Gradle) from the runtime environment (JRE 21).
* The solution must run via a single `docker-compose up --build` command.
* **Network:** Ensure the API can reach RabbitMQ and the Worker can reach both RabbitMQ and the Database.

---

## ✅ Evaluation Criteria
We will evaluate your submission based on:

1.  **Modern Java:**
    * Is the project correctly set up for Java 21?
    * **Are Virtual Threads actually active?** (We will check the logs).
2.  **API Standards:**
    * Is the API explicitly documented via Swagger/OpenAPI?
    * Is the spec accessible when running the container?
3.  **Distributed Logic:**
    * Is the `correlationId` passed correctly across the network?
    * Is the Idempotency implementation robust?
4.  **Code Quality:**
    * Clean, readable code with proper separation of concerns.
    * **Unit Tests** for critical business logic are required.
5.  **Containerization:**
    * Dockerfiles should be optimized (small image size).
    * Services must wait for dependencies (DB/Queue) to be ready.
6.  **AI Transparency:**
    * You are permitted to use AI tools.
    * If you do, document your prompts in `ai_instructions/` and explain your verification logic in `DECISIONS.md`.

## 📄 Documentation (`DECISIONS.md`)
Please include a `DECISIONS.md` file explaining:
1.  **Idempotency Strategy:** How did you prevent double-processing?
2.  **Virtual Threads:** How did you verify that Virtual Threads are handling the requests?
3.  **Resilience:** How are you handling the simulated failures?
4.  **API Documentation:** Which library did you use (e.g., `springdoc-openapi`) and why?

---

## 🚀 Submission Process

Please follow these steps to deliver your test. We value a clear Git history that shows your progress.

1.  **Clone the Repository:**
    Clone this repository to your local machine.
    ```bash
    git clone <repo_url>
    ```

2.  **Create a Branch:**
    Create a new branch using the naming convention `submission/firstname-lastname`.
    ```bash
    git checkout -b submission/john-doe
    ```

3.  **Commit Often:**
    We want to see your progression. Do not squash everything into one commit at the end.
    * *Bad:* "Done" (one massive commit)
    * *Good:* "Setup Spring Boot", "Implement Producer", "Add Unit Tests", "Refactor Dockerfile"

4.  **Open a Pull Request:**
    * Push your branch to the origin.
    * Open a **Pull Request (PR)** against the `main` branch.
    * In the PR description, give a brief summary of your work and link to any specific files you want us to look at first.

**Do not merge the PR yourself.** The review team will review the code directly in the PR.
