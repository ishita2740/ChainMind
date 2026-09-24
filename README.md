# ⚡ ChainMind — AI-Assisted Supply Chain Management Platform

**ChainMind** is a modular supply chain management platform that combines deterministic business logic with asynchronous AI-generated insights, covering inventory monitoring, demand forecasting, procurement, and logistics tracking.

![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![PostgreSQL](https://img.shields.io/badge/Neon_PostgreSQL-00E5A0?style=for-the-badge&logo=postgresql&logoColor=black)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Prophet](https://img.shields.io/badge/Forecasting-Prophet-0068B5?style=for-the-badge)
![Groq](https://img.shields.io/badge/LLM-Groq_LLaMA_3.3-F55036?style=for-the-badge)

---

## 📖 What ChainMind Does

Most dashboards that bolt on an LLM make a live model call on every page load, so every KPI and chart pays for inference latency even when nothing has changed. ChainMind separates the two concerns instead:

- **Deterministic layer** — KPIs that can be computed directly from the database (stock levels, PO counts, system-health scores, forecast values) are calculated in Python/SQL and returned without waiting on a model call.
- **AI layer** — Reasoning that benefits from an LLM (procurement recommendations, risk explanations) runs as a background task. The output is written to Postgres and read back on subsequent loads, rather than regenerated on every request.

## ✨ Key Features

- **Executive Dashboard** — KPIs and a system-health score computed from live inventory/procurement data.
- **Procurement Intelligence** — Persisted AI-generated reasoning alongside PO management.
- **Demand Forecasting** — Prophet-based forecasting with visual plotting per SKU.
- **Logistics Tracking** — Shipment milestones and route optimization via OSRM.
- **Inventory Control** — Safety-stock thresholds and stock-movement tracking.

---

## 🏗️ Architecture

```
ChainMind/
├── api/routes/          # FastAPI routers — one per domain (inventory, procurement, forecasting, logistics)
├── services/             # Business logic layer — deterministic engines + AI orchestration
├── schemas/                # Pydantic request/response models
├── migrations/               # Database schema migrations
├── data/                       # Datasets used for seeding & forecasting
├── models.py                    # SQLAlchemy ORM models (Neon Postgres)
├── database.py                    # DB engine/session management
├── main.py                          # FastAPI entry point & router registration
│
├── ai_agent.py                        # AI reasoning service (Groq / LLaMA 3.3)
├── ai_insight_service.py               # Orchestrates async AI insight generation & persistence
├── forecast_service.py / prophet_model.py   # Demand forecasting (Prophet)
├── data_preparation.py / evaluation.py         # Forecast data prep & accuracy evaluation
│
├── init_db.py / seed_db.py / seed_logistics.py / setup_suppliers.py  # DB lifecycle & sample data
├── requirements.txt
├── Dockerfile
│
└── frontend/                                    # React + Vite + TypeScript dashboard
    └── src/
        ├── components/  ├── pages/  ├── hooks/  └── services/
```

**Routes stay thin** — HTTP concerns and validation only. **Services own the logic** and don't depend on FastAPI, so they're independently testable. **The AI layer is decoupled** from the request/response cycle of core pages, so a slow or failed model call doesn't affect dashboard responsiveness.

`ai_agent.py` calls Groq's LLaMA 3.3 with deterministic context (stock levels, forecasts, lead times) to produce a reasoning output; `ai_insight_service.py` decides when to trigger that call and persists the result so it doesn't need to be regenerated on every page view.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Backend** | FastAPI, SQLAlchemy (eager loading via `joinedload`) |
| **Database** | Neon PostgreSQL (serverless) |
| **Frontend** | React 18, TypeScript, Vite |
| **Styling** | Tailwind CSS |
| **Forecasting** | Prophet |
| **AI reasoning** | Groq (LLaMA 3.3) |
| **Logistics** | OSRM (routing), Geopy |

---

## 🚀 Getting Started

### Prerequisites
- Python 3.10+
- Node.js 18+
- A Postgres connection string (Neon recommended)
- A Groq API key (for AI-insight features)

### 1. Clone & configure

```bash
git clone https://github.com/ishita2740/ChainMind.git
cd ChainMind
cp .env.example .env
```

Fill in `.env` with your own database URL, Groq API key, and any other values `config.py` expects. Make sure `.env` is listed in `.gitignore` and never committed.

### 2. Backend

```bash
python -m venv venv
# Windows: .\venv\Scripts\activate | Unix/macOS: source venv/bin/activate
pip install -r requirements.txt
python init_db.py
python seed_db.py   # optional: sample data
uvicorn main:app --reload --port 8000
```

### 3. Frontend

```bash
cd frontend
npm install
npm run dev
```

Backend: `http://localhost:8000` · Frontend: the Vite dev server URL printed in your terminal.

### 4. Docker (alternative)

The included `Dockerfile` can containerize the backend for deployment on any container-hosting platform. The frontend builds to static assets via `npm run build` and can be served separately.

---

## 🔌 API Documentation

Once the backend is running, interactive API docs are available at:

```
http://localhost:8000/docs
```

generated automatically from the `schemas/` Pydantic models and route definitions.

---

## 🗺️ Roadmap

- [ ] Expand automated test coverage into a unified CI suite
- [ ] Add role-based access control for procurement actions
- [ ] Extend forecasting with an XGBoost alternative alongside Prophet
- [ ] Build out proactive inventory alerting

---

## 📜 License

Internal / educational use. *(Replace with an actual license, e.g. MIT, if you intend this repo to be open source.)*
