# Clearway — AI-Powered Debt Relief & Financial Recovery Platform

An AI-assisted platform that helps borrowers track loans, understand their debt
stress, and generate lender-ready settlement negotiation letters.

**Stack:** React.js (Vite) · FastAPI · SQLite + SQLAlchemy · Google Gemini API

```
debt-relief-platform/
├── backend/        FastAPI app, SQLAlchemy models, Gemini integration
└── frontend/        React + Vite dashboard
```

## 1. Backend setup

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

cp .env.example .env
# edit .env: set SECRET_KEY and (optionally) GEMINI_API_KEY
```

Run the API:

```bash
uvicorn app.main:app --reload --port 8000
```

- API docs: http://localhost:8000/docs
- SQLite database file (`debt_relief.db`) is created automatically on first run.
- If `GEMINI_API_KEY` is left blank, the app still works end-to-end — negotiation
  letters and AI insights fall back to a rules-based template instead of calling Gemini.

## 2. Frontend setup

```bash
cd frontend
npm install
npm run dev
```

- App runs at http://localhost:5173
- The Vite dev server proxies `/api/*` requests to `http://localhost:8000`
  (see `vite.config.js`), so both servers need to be running together.

## 3. Using the app

1. Register an account, then log in.
2. Go to **Loans** and add each loan (lender, outstanding amount, EMI, overdue
   days, monthly income).
3. Visit **Financial Dashboard** to see your aggregate debt stress score and
   monthly surplus.
4. Visit **Settlement Predictor**, pick a loan, and run a prediction — this
   stores a history record you can revisit later.
5. Visit **AI Letter Generator**, pick a loan and tone, and generate a
   negotiation email you can copy and send to your lender.

## 4. How the "AI" works

- **Financial analysis & settlement math** (`backend/app/services/financial_analysis.py`)
  is a transparent, deterministic rules engine — EMI-to-income ratio, an overdue
  severity factor, and a debt-to-annual-income multiple combine into a 0-100
  debt stress score, which then drives a settlement percentage and strategy
  (lump sum / structured plan / hardship program).
- **Gemini AI** (`backend/app/services/gemini_service.py`) takes those numbers
  and turns them into a natural-language insight and a full negotiation letter
  tailored to tone (professional / firm / hardship-focused). If no API key is
  configured, or a Gemini call fails, the app falls back to a clear template so
  the feature never breaks the user experience.

## 5. Security notes for production

- Replace `SECRET_KEY` with a long random value and never commit `.env`.
- Swap SQLite for Postgres/MySQL by changing `DATABASE_URL`.
- Add HTTPS, rate limiting, and refresh tokens before deploying publicly.
- Restrict `CORS_ORIGINS` to your real frontend domain.
