# 🚀 MediSync HealthOS: Complete Startup Guide

This guide provides the exact commands and paths required to get the entire project running.

## 📋 Prerequisites
Ensure you have the following installed:
- **Node.js** (v20 or higher)
- **npm** (v10 or higher)
- **Docker Desktop** (running)
- **Python** (3.11 or higher)

---

## 🛠️ Step 1: Environment Configuration
You must set up your environment variables before starting the services.

1. **Path:** `c:\Users\Balraj\Downloads\medisync-platform-main\medisync-platform-main`
2. **Action:** Copy `.env.example` to `.env`.
   ```powershell
   copy .env.example .env
   ```
3. **Action:** Open `.env` and fill in your API keys (OpenAI, Anthropic, etc.).

---

## 📦 Step 2: Install Dependencies
Install all required packages for the Node.js monorepo and the Python AI service.

### Node.js Monorepo
1. **Path:** `c:\Users\Balraj\Downloads\medisync-platform-main\medisync-platform-main`
2. **Command:**
   ```powershell
   npm install
   ```

### AI Service (Python)
1. **Path:** `c:\Users\Balraj\Downloads\medisync-platform-main\medisync-platform-main\services\ai-service`
2. **Command:**
   ```powershell
   pip install -r requirements.txt
   ```

---

## 🐳 Step 3: Start Infrastructure
Launch the required databases and message queues (PostgreSQL, MongoDB, Redis, RabbitMQ, Elasticsearch) using Docker.

1. **Path:** `c:\Users\Balraj\Downloads\medisync-platform-main\medisync-platform-main`
2. **Command:**
   ```powershell
   npm run docker:up
   ```

---

## 🗄️ Step 4: Initialize the Database
Prepare the primary database with the required schema and seed data.

1. **Path:** `c:\Users\Balraj\Downloads\medisync-platform-main\medisync-platform-main\services\medisync-service`
2. **Commands:**
   ```powershell
   npx prisma migrate dev --name init
   npx prisma db seed
   ```

---

## ⚡ Step 5: Start the Platform
Run all services simultaneously using Turborepo.

1. **Path:** `c:\Users\Balraj\Downloads\medisync-platform-main\medisync-platform-main`
2. **Command:**
   ```powershell
   npm run dev
   ```

> [!NOTE]
> The `npm run dev` command will clear ports and start the API Gateway, Services, and Frontend. If the AI service does not start automatically, run `python main.py` in the `services/ai-service` directory.

---

## ✅ Verification
Once everything is running, you can access the following:

| Service | Local URL |
| :--- | :--- |
| **User Interface (Frontend)** | [http://localhost:4000](http://localhost:4000) |
| **API Gateway** | [http://localhost:3000](http://localhost:3000) |
| **API Documentation** | [http://localhost:3000/api/docs](http://localhost:3000/api/docs) |
| **AI Service** | [http://localhost:8000](http://localhost:8000) |
| **RabbitMQ Management** | [http://localhost:15672](http://localhost:15672) |
