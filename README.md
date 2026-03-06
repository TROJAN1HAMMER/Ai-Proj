# AI-Driven Vulnerability Intelligence Platform

> A full-stack security intelligence platform that predicts real-world exploit likelihood and prioritizes vulnerabilities dynamically, going beyond static CVSS scores using Machine Learning and Explainable AI.

![Dashboard Preview](https://via.placeholder.com/1200x600.png?text=AI+Vulnerability+Intelligence+Dashboard)

## 📋 Overview

Modern vulnerability management suffers from "alert fatigue" because almost everything is graded "High" or "Critical" by traditional CVSS metrics. This platform solves that by:
1. Ingesting raw vulnerability scans (JSON payloads).
2. Processing them through a custom **XGBoost Machine Learning model** trained on historic vulnerability and exploitation data (NVD & KEV).
3. Predicting the true **Exploit Probability** based on environmental context (like internet exposure) and technical characteristics.
4. Explaining *why* a vulnerability was prioritized using **SHAP (SHapley Additive exPlanations)**.

## ✨ Features

- **Microservice Architecture**: Decoupled Python ML engine, Node.js API orchestrator, and React frontend.
- **Dynamic Prioritization**: Scores reflect real-world exploit likelihood rather than static theoretical risk.
- **Explainable AI (XAI)**: Drop-down XAI insights directly in the dashboard show exactly which factors (e.g., *Exploit Available*, *Internet Exposed*) contributed positively or negatively to the risk score.
- **Automated PDF Reports**: 1-click generation of PDF remediation reports.
- **Simulation Console**: Built-in mock data generators to instantly test Max, Med, and Min risk payloads.
- **Responsive Dark-Mode UI**: Built with Tailwind CSS and Recharts for a premium, mobile-ready command center experience.

---

## 🏗 System Architecture

1. **ML Service (`/ml-service`)**
   - **Tech**: Python, FastAPI, Pandas, Scikit-learn, XGBoost, SHAP.
   - **Role**: Calculates Exploit Probabilities and generates feature-impact explanations via `/predict` and `/explain` REST endpoints.
2. **Backend API (`/backend-node`)**
   - **Tech**: Node.js, Express, Sequelize (SQLite), JWT, PDFKit.
   - **Role**: Secure orchestrator. Handles user Auth, persists assets/vulnerabilities, communicates with the ML Service, and serves dashboard data.
3. **Frontend Dashboard (`/dashboard`)**
   - **Tech**: React, Vite, Tailwind CSS, Recharts, HeadlessUI.
   - **Role**: The centralized, responsive user interface.

---

## 🚀 Quick Start Guide

You will need three separate terminal windows to run all microservices locally.

### 1. Start the Machine Learning Service
```bash
cd ml-service
# Activate your virtual environment (Windows example)
.\venv\Scripts\Activate.ps1
# (Or on Mac/Linux: source venv/bin/activate)

# Install dependencies if you haven't already
# pip install -r requirements.txt

# Run the FastAPI server
uvicorn prediction_api:app --host 0.0.0.0 --port 8000 --reload
```

### 2. Start the Node.js API Core
```bash
cd backend-node

# Install dependencies if you haven't already
# npm install

# Seed the database with the default admin user (Run this once)
node -e "const bcrypt = require('bcryptjs'); const db = require('./models'); bcrypt.genSalt(10).then(salt => bcrypt.hash('password123', salt)).then(hash => db.User.create({username: 'admin', password_hash: hash, role: 'admin'})).then(() => console.log('User created: admin / password123')).catch(console.error);"

# Start the Express server
npm run dev
```

### 3. Start the React Dashboard
```bash
cd dashboard

# Install dependencies if you haven't already
# npm install

# Start the Vite development server
npm run dev
```

---

## 🖥 Usage

1. Open your browser to [http://localhost:5173](http://localhost:5173).
2. Login with the seeded credentials:
   - **Username**: `admin`
   - **Password**: `password123`
3. Navigate to the **Scan Upload** tab.
4. Click one of the template buttons (e.g., **High Risk / Max**) to load simulated scan data.
5. Click **Ingest & Analyze**. The platform will fetch predictions from the Python backend and store them.
6. Navigate to the **Overview** and **Vulnerabilities** tabs to explore the localized threat intelligence, including SHAP explanations!
7. Click **Generate PDF Report** to export your priority remediation list.

---

## 🛡 License
This project is an experimental prototype for AI-driven cybersecurity prioritization.
