# AI-Driven Vulnerability Intelligence Platform

> A full-stack security intelligence platform that predicts real-world exploit likelihood and prioritizes vulnerabilities dynamically. It goes beyond static CVSS scores by leveraging Machine Learning (XGBoost) and Explainable AI (SHAP) to provide actionable, context-aware risk intelligence.

## Overview

Modern vulnerability management suffers from "alert fatigue" because traditional CVSS metrics grade almost everything as "High" or "Critical," lacking environmental context. This platform solves that critical flaw by:
1. **Ingesting** raw vulnerability scans (via direct JSON payloads simulating output from scanners like OpenVAS or Nessus).
2. **Processing** the vulnerabilities through a custom-trained **XGBoost Machine Learning model**. The model evaluates historical vulnerability traits (from NVD and KEV catalogs).
3. **Predicting** the true **Exploit Probability** based on environmental context (like internet exposure, containerization) and technical characteristics (attack vectors, privileges).
4. **Explaining** exactly *why* a vulnerability was prioritized using **SHAP (SHapley Additive exPlanations)**, mapping AI reasoning directly into human-readable insights.

---

## Key Features

- **Microservice Architecture**: Fully decoupled environments utilizing a Python ML engine, Node.js API orchestrator, and React frontend proxy.
- **Dynamic Prioritization**: Risk scores reflect real-world exploit likelihood rather than static theoretical risk. Tiers (Critical, High, Medium, Low) are bounded by ML probabilities.
- **Explainable AI (XAI)**: Drop-down XAI insights directly in the dashboard show exactly which factors (e.g., *Exploit Available*, *Internet Exposed*) contributed positively (increasing risk) or negatively (decreasing risk) to the final prediction.
- **Automated PDF Reports**: 1-click dynamic generation of PDF remediation reports listing prioritized assets.
- **Simulation Console**: Built-in mock data generators in the Scan Upload feature to instantly test Max, Med, and Min risk payloads.
- **Responsive Dark-Mode UI**: Built with Tailwind CSS and Recharts to offer a premium, mobile-ready command center experience.
- **Secure Authentication**: Stateless JWT-based authentication system safeguarding the API interfaces.

---

## System Architecture & Directory Structure

The repository is modularized into three core services communicating over REST logic.

```text
ai-vulnerability-intelligence/
├── backend-node/      # The API Orchestrator (Node.js/Express)
├── dashboard/         # The User Interface (React/Vite)
├── ml-service/        # The AI Engine (Python/FastAPI)
├── dataset/           # Mock raw data for testing and mapping
└── README.md          # Project Documentation
```

### 1. ML Service (`/ml-service`)
Responsible for executing Data Science and Machine Learning capabilities. 
- **Tech Stack**: Python 3, FastAPI, Pandas, Scikit-learn, XGBoost, SHAP.
- **Role**: Calculates Exploit Probabilities and generates feature-impact explanations.
- **Core Endpoints**: 
  - `POST /predict`: Accepts an array of vulnerabilities and returns a mapping of exploit probabilities.
  - `POST /explain`: Accepts a single vulnerability profile and returns the top 5 SHAP factors that influenced its specific score.
- **Scripts**: Contains `train_model.py` to regenerate the XGBoost `.pkl` files and `feature_engineering.py` for transforming semantic strings into localized numerical arrays.

### 2. Backend API (`/backend-node`)
The central nervous system of the platform.
- **Tech Stack**: Node.js, Express, Sequelize (SQLite), jsonwebtoken, PDFKit.
- **Role**: Secure orchestrator. Handles user Auth, persists assets and vulnerabilities structurally into the local database, acts as a gateway to the ML Service, and hydrates the dashboard dashboard graphs.
- **Core Controllers**:
  - `authController`: Issues JWT tokens for security.
  - `scanController`: Parses vulnerability datasets, queries the ML service, and writes enhanced data to SQLite.
  - `dashboardController`: Generates real-time aggregation stats (asset count, risk tier pie-chart maps).
  - `reportController`: Dynamically draws a PDF file stream using PDFKit detailing vulnerabilities optimized for remediation.

### 3. Frontend Dashboard (`/dashboard`)
The visual presentation layer.
- **Tech Stack**: React 18, Vite, Tailwind CSS (v4), Recharts, HeadlessUI, Axios.
- **Role**: The centralized, responsive user interface. Handles local JWT caching, React Router routing, and Tailwind responsive flex-box structures to accommodate Mobile, Tablet, and Desktop screen widths.

---

## Quick Start Guide

You will need three separate terminal windows to run all microservices locally.

### 1. Start the Machine Learning Service (Terminal 1)
```bash
cd ml-service

# Activate your virtual environment (Windows example)
.\venv\Scripts\Activate.ps1
# (Or on Mac/Linux: source venv/bin/activate)

# Install requirements (if running for the first time)
# pip install -r requirements.txt

# Run the FastAPI server
uvicorn prediction_api:app --host 0.0.0.0 --port 8000 --reload
```

### 2. Start the Node.js API Core (Terminal 2)
```bash
cd backend-node

# Install dependencies
# npm install

# Seed the database with the default admin user (Run this once to create your login)
node -e "const bcrypt = require('bcryptjs'); const db = require('./models'); bcrypt.genSalt(10).then(salt => bcrypt.hash('password123', salt)).then(hash => db.User.create({username: 'admin', password_hash: hash, role: 'admin'})).then(() => console.log('User created: admin / password123')).catch(console.error);"

# Start the Express server
npm run dev
```

### 3. Start the React Dashboard (Terminal 3)
```bash
cd dashboard

# Install dependencies
# npm install

# Start the Vite development server
npm run dev
```

---

## Usage Walkthrough

1. Open your browser to **[http://localhost:5173](http://localhost:5173)**.
2. Login with the seeded credentials:
   - **Username**: `admin`
   - **Password**: `password123`
3. Navigate to the **Scan Upload** tab on the navigation menu.
4. Click one of the template buttons (**High Risk / Max**, **Medium Risk / Med**, or **Low Risk / Min**) to load a simulated scan data JSON block into the text interface.
5. Click **Ingest & Analyze**. The platform will silently interface with the Node backend, which requests an ML evaluation from the Python backend, returning the database entities.
6. Navigate to the **Overview** tab to see your organizational vulnerability health (interactive Pie charts and numerical aggregates).
7. Navigate to the **Vulnerabilities** tab to see the ranked list. Click any row in the list to trigger the Explainable AI (SHAP) readout, indicating exactly *why* the AI assigned that percentage risk.
8. Back on the Overview tab, click **Generate PDF Report** to export your priority remediation list.

---

## License & Disclaimer
This project is an experimental prototype intended for AI-driven cybersecurity prioritization research. Do not upload actual production vulnerability data strings to unstructured/untrusted environments without auditing network encryption paths.
