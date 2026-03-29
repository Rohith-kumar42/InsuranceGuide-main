# Technical Architecture & Workflow: Insurance Recommendation Engine

## 1. Project Overview
### 1.1 Purpose
The Insurance Recommendation Engine is a sophisticated, data-driven platform designed to eliminate information asymmetry in the insurance market. By aggregating complex policy data across multiple categories (Health, Life, Motor, Travel, etc.) and aligning it with individual user metrics, the system provides objective, transparent, and personalized insurance suggestions.

### 1.2 Core Problem Solved
Traditional insurance discovery is plagued by:
- **Opaque Pricing**: Complex riders and hidden terms make apple-to-apple comparisons difficult.
- **Biased Guidance**: Agent-driven models often prioritize commission over coverage.
- **Data Overload**: Thousands of products with varying limits, deductibles, and waiting periods overwhelm consumers.

The platform solves this by acting as an autonomous technical layer that parses thousands of data points to find the "mathematical best" fit for a user's risk profile.

---

## 2. Complete Tech Stack
The system is built on a modern, distributed architecture designed for scalability, high availablity, and heavy data ingestion.

### 2.1 Frontend Tier
- **Framework**: Next.js 15 (React 19)
- **State Management**: React Server Components (RSC) + Client-side Hooks
- **Styling**: Tailwind CSS 4.0 (Utilizing the latest JIT engine for performance)
- **Type Safety**: TypeScript 5.x

### 2.2 Backend (BFF - Backend for Frontend)
- **Runtime**: Node.js 20+
- **Framework**: Express.js
- **ORM**: Sequelize (PostgreSQL/SQLite)
- **Communcation**: RESTful APIs + WebSocket (for real-time chat/scraping status)

### 2.3 AI & Recommendation Layer
- **Core Engine**: Python 3.11 (FastAPI)
- **Machine Learning**: Scikit-Learn (Random Forest/XGBoost for ranking)
- **Natural Language**: OpenAI GPT-4o (via API) for the "Insurance Advisor" chatbot
- **Data Mining**: Playwright (Stealth mode) for real-time market data extraction

### 2.4 Data Tier
- **PostgreSQL**: Primary transactional database (User profiles, recommendations, auth)
- **MongoDB**: Unstructured policy document storage and knowledge base
- **Redis**: Caching layer for session management and ephemeral recommendation results
- **SQLite**: Local developer-mode database for rapid prototyping

---

## 3. System Architecture
The platform follows a decoupled, microservices-oriented architecture represented by the following logical blocks:

### 3.1 Presentation Layer (Frontend)
A server-side rendered (SSR) Next.js application that ensures high SEO performance for education content while providing a dynamic, single-page experience for the recommendation quiz and comparison tools.

### 3.2 Gateway/BFF Layer (Backend API)
The Node.js backend serves as a "BFF" (Backend for Frontend). It handles:
- **Authentication**: JWT-based stateless auth with OTP verification.
- **Orchestration**: Aggregating data from the database and Python AI service.
- **Data Normalization**: Ensuring the frontend receives consistent models regardless of the underlying data source.

### 3.3 Intelligence Layer (Python Service)
The "Brain" of the system. This service is isolated from structural concerns and focuses purely on:
- **Ranking**: Scoring 1,000+ insurance variants against a 20-point user metric vector.
- **NLP**: Processing natural language queries for the advisor tool.

---

## 4. Recommendation Engine Logic
The engine utilizes a multi-pass processing pipeline to ensure both accuracy and performance.

### 4.1 Pass 1: Canonical Eligibility Filtering
Before ranking, the engine applies hard constraints:
- **Age**: Removing plans with entry-age limits outside the user's range.
- **Location**: Filtering plans not serviced in the user's PIN code.
- **Medical History**: High-level exclusion based on pre-existing conditions (PED).

### 4.2 Pass 2: Feature Weighting (Vector Matching)
The user's input is converted into a preference vector. Product features (like "Restoration Benefit" or "No Room Rent Limit") are weighted:
- **High Weight (0.8 - 1.0)**: Essential requirements (e.g., "Must cover maternity").
- **Medium Weight (0.4 - 0.7)**: Preferred features (e.g., "Prefer high CSR").
- **Low Weight (0.1 - 0.3)**: Tie-breakers (e.g., "Brand reputation").

### 4.3 Pass 3: The Scoring Algorithm
The score for product $P$ is calculated as:
$$Score(P) = \sum (Weight_i \times Subscore_i)$$
Where $Subscore_i$ is a normalized value (0 to 1) comparing a specific feature of $P$ to the user's ideal requirement.

---

## 5. Insurance Data Layer
The data layer is designed for extreme flexibility to accommodate the constantly changing insurance landscape.

### 5.1 Data Ingestion (Scraper)
- **Stealth Architecture**: Uses `playwright-stealth` to bypass bot detection on major insurance aggregators.
- **ETL Pipeline**:
    1. **Extract**: Raw HTML/JSON from provider sites.
    2. **Transform**: Normalizing currency formats ("Lakhs" to numeric), benefit strings, and coverage periods.
    3. **Load**: Upserting into PostgreSQL with conflict resolution based on `(provider, product, sum_insured)`.

### 5.2 Schema Strategy
Rather than a rigid schema, the engine stores core metrics (Premium, Cover, CSR) in indexed columns and complex "Special Benefits" as a JSONB field. This allows the recommendation engine to parse new feature types without requiring database migrations.

---

## 6. End-to-End Workflow

### 6.1 User Onboarding & Profiling
1. User interacts with the **Profile Questionnaire**.
2. Frontend sends metrics (Age, Family Size, City, Habits) to `POST /api/recommendation/profile`.
3. Backend validates and stores the profile, then triggers the AI service.

### 6.2 Recommendation Generation
1. AI service fetches active products from the DB.
2. It passes the User Profile + Product List through the ranking model.
3. Top 10 results are returned to the BFF, enriched with full product metadata, and sent to the UI.

### 6.3 Knowledge & Education
1. User browses the **Knowledge Hub**.
2. Requests are routed through the AI Advisor which leverages the MongoDB knowledge base to explain terms like "Co-payment" or "Sub-limits" in the context of specific recommended plans.

---

## 7. Integration Points
- **OpenAI API**: Powers the conversational interface, providing context-aware answers based on the scraped policy documents.
- **Nodemailer**: Integrated for sending personalized PDF summaries of the recommendation results.
- **Third-Party APIs**: Capability to fetch real-time quotes via direct insurer APIs (where available) to supplement scraped data.

---

## 8. Security and Authentication
### 8.1 Data Protection
- **PII Encryption**: Sensitive data like contact numbers and medical history are encrypted at rest using AES-256.
- **Stateless Session**: JWTs are stored in `HTTPOnly` cookies to prevent XSS-based token theft.

### 8.2 Authentication Flow
The system uses a two-factor approach for high-value actions:
1. **Primary**: Email/Mobile login.
2. **Secondary**: OTP (One-Time Password) generation and validation layer.

---

## 9. Deployment and Infrastructure
The system is fully containerized using Docker, allowing for "Write Once, Run Anywhere" capability.

### 9.1 Container Strategy
- **`app-frontend`**: Next.js service.
- **`app-backend`**: Node.js service.
- **`ai-service`**: Python FastAPI service.
- **`db-postgres`**: Persistent data storage.
- **`db-redis`**: Caching.

### 9.1 Scalability
The Python AI service is stateless, allowing it to be horizontally scaled behind a load balancer during peak traffic (e.g., tax-saving seasons in March).

---

## 10. Summary
This architecture provides a robust, scalable, and highly intelligent foundation for insurance discovery. By separating concerns between the data ingestion, recommendation logic, and user interface, the system ensures that it can adapt to future insurance products while maintaining a world-class user experience.
