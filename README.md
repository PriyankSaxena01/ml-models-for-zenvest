# Zenvest Integration README

## Goal

This README explains how to connect the **frontend**, **backend**, and **ML services** for Zenvest so the full flow works during demo and submission.

---

## 1. Recommended project structure

```text
zenvest/
  frontend/
    src/
      pages/
      components/
      services/
        api.js
      utils/
    package.json

  backend/
    src/
      routes/
        riskRoutes.js
        fraudRoutes.js
      controllers/
        riskController.js
        fraudController.js
      services/
        mlService.js
      app.js
      server.js
    package.json
    .env

  ml-service/
    app/
      main.py
      train.py
      predict.py
      schema.py
      fraud_train.py
      fraud_predict.py
      fraud_schema.py
    models/
      risk_model.json
      label_encoders.pkl
      fraud_model.pkl
      fraud_columns.pkl
    data/
      synthetic_risk_data.csv
      synthetic_fraud_data.csv
    requirements.txt
```

---

## 2. What each part does

### Frontend

Collects user data and sends it to backend.

### Backend

Handles logic, calls ML service, and returns results.

### ML Service

Runs trained models and returns predictions.

---

## 3. Integration flow

### Risk scoring

Frontend → Backend → ML → Backend → Frontend

### Fraud detection

Frontend → Backend → ML → Backend → Decision

---

## 4. ML service setup

```bash
cd ml-service
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

Train models:

```bash
python -m app.train
python -m app.fraud_train
```

Run service:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

---

## 5. Backend setup

```bash
cd backend
npm install
```

Create `.env`:

```env
PORT=5000
ML_SERVICE_URL=http://127.0.0.1:8000
```

Run backend:

```bash
node src/server.js
```

---

## 6. Frontend setup

```bash
cd frontend
npm install
npm run dev
```

---

## 7. API configuration

```js
// frontend/src/services/api.js
import axios from "axios";

const API = axios.create({
  baseURL: "http://localhost:5000/api",
});

export const predictRisk = (data) => API.post("/risk/predict", data);
export const checkFraud = (data) => API.post("/fraud/check", data);
```

---

## 8. How to Run (Same Device)

Run in order:

### Terminal 1

```bash
cd ml-service
venv\Scripts\activate
uvicorn app.main:app --reload
```

### Terminal 2

```bash
cd backend
node src/server.js
```

### Terminal 3

```bash
cd frontend
npm run dev
```

URLs:

* Frontend: http://localhost:5173
* Backend: http://localhost:5000
* ML: http://127.0.0.1:8000

---

## 9. How to Run (Same WiFi - Multiple Devices)

1. Find IP using:

```bash
ipconfig
```

Example: `192.168.1.5`

2. Update backend `.env`:

```env
ML_SERVICE_URL=http://192.168.1.5:8000
```

3. Update frontend:

```js
baseURL: "http://192.168.1.5:5000/api"
```

4. Run ML:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

5. Run backend with:

```js
app.listen(PORT, "0.0.0.0");
```

6. Access frontend from other device:

```
http://192.168.1.5:5173
```

---

## 10. How to Use

### Risk Prediction

* Fill onboarding form
* Submit → get:

  * Risk label
  * Recommended plan

### Fraud Detection

* Submit claim data
* Output:

  * Fraud suspected or not
  * Risk level
  * Action (approve/manual review)

---

## 11. Test APIs directly

Swagger:

```
http://127.0.0.1:8000/docs
```

---

## 12. Startup Order

1. ML service
2. Backend
3. Frontend

---

## 13. Common Issues

* Backend not connecting → check ML URL
* Frontend not connecting → check baseURL
* Models missing → run training scripts

---

## 14. Architecture Rule

Always follow:

```
Frontend → Backend → ML
```

(Not directly frontend → ML)

---

## 15. Deployment Plan (Future Scope)

This project currently runs locally or on same WiFi, but is designed for deployment.

### Planned Architecture

```
Frontend (Vercel / Netlify)
        ↓
Backend (Render / Railway / AWS)
        ↓
ML Service (AWS / GCP / Docker)
```

### Deployment Changes Required

* Replace `localhost` with public URLs
* Configure environment variables
* Enable CORS
* Add authentication (future)

### Current Status

* ✔ Works locally
* ✔ Works on same WiFi
* 🚧 Deployment planned next

---

## 16. Final Summary

* Frontend → UI
* Backend → logic + API
* ML → predictions

This setup is:

* modular
* scalable
* demo-ready
* production-friendly

---
