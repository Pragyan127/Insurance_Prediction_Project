# 🚗 Vehicle Data MLOps Project

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-green?logo=fastapi)
![Docker](https://img.shields.io/badge/Docker-Containerized-blue?logo=docker)
![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20S3%20%7C%20ECR-orange?logo=amazonaws)
![MongoDB](https://img.shields.io/badge/MongoDB-Atlas-green?logo=mongodb)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-black?logo=githubactions)

An end-to-end **production-grade MLOps pipeline** for vehicle data prediction — covering everything from data ingestion to automated model deployment on AWS using CI/CD.

---

## 📌 Project Overview

This project demonstrates a fully automated machine learning lifecycle with modular components for data ingestion, validation, transformation, model training, evaluation, and deployment. The app is containerized, hosted on AWS EC2, and continuously deployed via GitHub Actions — a complete MLOps workflow from raw data to live prediction API.

---

## 🏗️ Architecture

```
MongoDB Atlas (Raw Data)
        │
        ▼
  Data Ingestion
        │
        ▼
  Data Validation  ──► Schema Check (schema.yaml)
        │
        ▼
Data Transformation
        │
        ▼
  Model Training   ──► Custom Estimator Pipeline
        │
        ▼
 Model Evaluation  ──► Threshold-based Comparison
        │
        ▼
  Model Pusher     ──► AWS S3 Model Registry
        │
        ▼
 FastAPI App (Prediction + Training Routes)
        │
        ▼
  Docker Container ──► AWS ECR ──► AWS EC2
        │
        ▼
    GitHub Actions (CI/CD Pipeline)
```

---

## 🔧 Tech Stack

| Category | Tools & Services |
|---|---|
| **Language** | Python 3.10 |
| **Web Framework** | FastAPI + Uvicorn |
| **Templating** | Jinja2 |
| **Database** | MongoDB Atlas |
| **ML Pipeline** | Scikit-learn, Custom Estimator |
| **Cloud Storage** | AWS S3 |
| **Container Registry** | AWS ECR |
| **Compute** | AWS EC2 (Ubuntu 24.04) |
| **Containerization** | Docker |
| **CI/CD** | GitHub Actions (Self-hosted Runner) |
| **Package Management** | Conda + pip |
| **Project Structure** | setup.py + pyproject.toml (local packages) |
| **Logging & Exceptions** | Custom logger + exception handler |

---

## 🚀 Key Features

- **Modular Pipeline Architecture** — Each ML stage (ingestion → validation → transformation → training → evaluation → pusher) is a standalone, reusable component
- **Automated CI/CD** — Every `git push` triggers a full build, Docker image push to ECR, and deployment to EC2 via GitHub Actions
- **Model Registry on S3** — Trained models are versioned and stored in AWS S3; evaluation uses a threshold score to decide whether to push a new model
- **MongoDB Integration** — Raw vehicle data is fetched from MongoDB Atlas, transformed into a DataFrame, and fed into the pipeline
- **Schema Validation** — A `schema.yaml` file defines expected data structure; the pipeline validates incoming data automatically
- **Live Prediction & Training Routes** — The deployed FastAPI app exposes `/` for predictions and `/training` to trigger a full pipeline rerun on demand
- **Containerized Deployment** — The app runs in a Docker container, mapped and accessible via public EC2 IP

---

## 📂 Project Structure

```
├── src/
│   ├── components/
│   │   ├── data_ingestion.py
│   │   ├── data_validation.py
│   │   ├── data_transformation.py
│   │   ├── model_trainer.py
│   │   ├── model_evaluation.py
│   │   └── model_pusher.py
│   ├── configuration/
│   │   ├── mongo_db_connections.py
│   │   └── aws_connection.py
│   ├── data_access/
│   ├── entity/
│   │   ├── config_entity.py
│   │   ├── artifact_entity.py
│   │   └── s3_estimator.py
│   ├── aws_storage/
│   ├── pipeline/
│   │   └── training_pipeline.py
│   └── utils/
│       └── main_utils.py
├── config/
│   └── schema.yaml
├── templates/
│   └── vehicledata.html
├── static/
├── notebook/
│   ├── EDA.ipynb
│   ├── feature_engineering.ipynb
│   └── mongoDB_demo.ipynb
├── app.py
├── Dockerfile
├── .dockerignore
├── requirements.txt
├── setup.py
├── pyproject.toml
└── .github/
    └── workflows/
        └── aws.yaml
```

---

## ⚙️ Setup & Installation

### 1. Clone the Repository
```bash
git clone https://github.com/<your-username>/vehicleproj.git
cd vehicleproj
```

### 2. Create & Activate Conda Environment
```bash
conda create -n vehicle python=3.10 -y
conda activate vehicle
pip install -r requirements.txt
```

### 3. Set Environment Variables
```bash
# MongoDB
export MONGODB_URL="mongodb+srv://<username>:<password>@cluster.mongodb.net"

# AWS Credentials
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
```

### 4. Run Locally
```bash
python app.py
```
Visit: `http://localhost:5001`

---

## 🐳 Docker

```bash
# Build
docker build -t vehicleproj .

# Run
docker run -d -p 5001:5001 vehicleproj
```

---

## ☁️ AWS Infrastructure

| Service | Purpose |
|---|---|
| **EC2** (Ubuntu 24.04, T2 Medium) | Hosts the Docker container |
| **ECR** | Stores Docker images (`vehicleproj`) |
| **S3** (`my-model-mlopsproj`) | Model registry — stores trained models |
| **IAM** | Scoped access credentials for CI/CD |

---

## 🔄 CI/CD Pipeline (GitHub Actions)

The pipeline is triggered on every push to the main branch:

1. **Build** — Docker image is built from the latest code
2. **Push** — Image is pushed to AWS ECR
3. **Deploy** — Self-hosted GitHub Actions runner on EC2 pulls and restarts the container

### GitHub Secrets Required

| Secret | Description |
|---|---|
| `AWS_ACCESS_KEY_ID` | IAM user access key |
| `AWS_SECRET_ACCESS_KEY` | IAM user secret key |
| `AWS_DEFAULT_REGION` | `us-east-1` |
| `ECR_REPO` | ECR repository URI |

---

## 🌐 API Endpoints

| Route | Method | Description |
|---|---|---|
| `/` | GET | Renders the vehicle prediction UI |
| `/training` | GET | Triggers the full ML training pipeline |

---

## 📊 ML Pipeline Details

- **Data Source:** MongoDB Atlas (vehicle dataset)
- **Validation:** Schema-based column and type checks
- **Transformation:** Feature engineering via custom transformer (saved as `.pkl`)
- **Model:** Trained with Scikit-learn; wrapped in a custom `VehicleModel` estimator
- **Evaluation:** New model is only pushed if it beats the existing S3 model by a threshold of `0.02`
- **Model Storage:** Pushed to S3 under `model-registry/` key

---

## 📓 Notebooks

| Notebook | Description |
|---|---|
| `EDA.ipynb` | Exploratory Data Analysis on vehicle dataset |
| `feature_engineering.ipynb` | Feature selection, encoding, and transformation experiments |
| `mongoDB_demo.ipynb` | Demonstrates pushing and reading data from MongoDB Atlas |

---

## 🙌 Acknowledgements

Built as a complete end-to-end MLOps project covering the full ML engineering lifecycle — from data pipelines and model tracking to containerization and automated cloud deployment.

---

## 📄 License

This project is for educational and portfolio purposes.