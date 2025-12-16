# Coding Challenge: Distributed Order Processing System

## 🎯 Objective
Design and implement a distributed system in Java that processes e-commerce orders asynchronously. 

**This is a Mid-Level role assessment.** We are looking for clean code, solid engineering fundamentals (idempotency, resilience), and the ability to produce production-ready containerized artifacts.

## ⏳ Time Limit
You have **2 weeks** to complete this challenge.

## 🛠 Technical Requirements

### 1. The Stack
* **Java 17+** (Spring Boot recommended).
* **RabbitMQ** (Messaging).
* **PostgreSQL** or **MySQL** (Persistence).
* **Docker & Docker Compose** (Containerization).

### 2. System Architecture



#### Service A: Order API (Producer)
* **Endpoint:** `POST /api/v1/orders`
* **Payload:** `{ "id": "uuid", "item": "String", "price": 100.50 }`
* **Logic:** * Validate the payload (price must be positive).
    * Generate a **`correlationId`** (UUID) for tracing.
    * Send the order + `correlationId` to RabbitMQ.
* **Resilience:** If RabbitMQ is down, the API should return a 500 or handle it gracefully.

#### Service B: Order Worker (Consumer)
* **Logic:**
    * Listen to the queue.
    * Process the order (simulate a delay of 2 seconds).
    * **Idempotency (CRITICAL):** Ensure that if a message is redelivered, the order is **not** processed/saved twice.
    * Save the order to the Database with status `PROCESSED`.
* **Simulated Failure:** If the order `id` contains the text `"FAIL"`, the worker must throw an exception.
* **Resilience:** * The worker **must not crash** on exceptions.
    * Failed messages must be logged with the `correlationId` and stack trace.

### 3. Containerization Requirements (Important)
You must containerize both Java applications.
* Create a `Dockerfile` for `order-api` and `order-worker`.
* **Best Practice:** We expect **Multi-Stage Builds** (build in one layer, run in a lighter JRE layer) to keep image sizes optimized.
* Update the `docker-compose.yml` so that running `docker-compose up` builds the apps and starts the **entire** environment (Apps + RabbitMQ + DB) in one command.

---

## ✅ Evaluation Criteria
We will evaluate your submission based on:

1.  **Distributed Logic:** * Is the `correlationId` passed correctly across the network and logs?
    * Is the Idempotency implementation robust?
2.  **Code Quality:** * Is the code clean, readable, and well-structured?
    * Are there **Unit Tests** for the critical business logic?
3.  **Containerization:** * Are the Dockerfiles optimized? 
    * Does the system spin up cleanly with `docker-compose up --build`?
4.  **AI Transparency:** * You are permitted to use AI tools (ChatGPT, Copilot, etc.). 
    * If you do, you **must** document your prompts in `ai_instructions/` and explain your verification logic.
    * Blindly copying AI code without understanding will likely fail the review.

## 📄 Documentation
Please include a `DECISIONS.md` file explaining:
1.  **Idempotency Strategy:** How did you prevent double-processing?
2.  **Docker Strategy:** Why did you structure the Dockerfiles this way?
3.  **Resilience:** How are you handling the simulated failures?

---
Good luck!
