# 🚀 End-to-End Enterprise MLOps on AWS

![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Amazon SageMaker](https://img.shields.io/badge/Amazon%20SageMaker-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Pytest](https://img.shields.io/badge/Pytest-0A9EDC?style=for-the-badge&logo=pytest&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)

**Author:** Seerat Fatima  
**Student ID:** 3840392051012  
**Course:** Artificial Intelligence (Batch IV) — NAVTTC  
**AWS Region:** `ap-southeast-2` (Sydney)  

---

## 📌 Architecture Overview

```mermaid
flowchart TD
    subgraph Data_Storage ["1. Data & Containers"]
        S3["Amazon S3: s3://seerat-mlops-bucket-2026/data/iris.csv"]
        ECR["Amazon ECR: mlops-iris-inference"]
    end

    subgraph CI_CD ["2. Automated Testing & CI/CD"]
        Pytest["test_ml_pipeline.py (3 Quality Gates)"]
        BuildSpec["buildspec.yml (AWS CodeBuild)"]
        Pytest --> BuildSpec
    end

    subgraph Serverless_Trigger ["3. Automated Event Trigger"]
        EventBridge["Amazon EventBridge Rule"]
        Lambda["AWS Lambda Retraining Trigger"]
        S3 -.->|New Data PutObject| EventBridge
        EventBridge -->|Invoke| Lambda
    end

    subgraph SageMaker_DAG ["4. SageMaker 5-Step Pipeline DAG"]
        Step1["PreprocessData (80/20 Train/Test Split)"]
        Step2["TrainModel (ml.m5.large Random Forest)"]
        Step3["EvaluateModel (evaluation.json)"]
        Step4{"Accuracy >= 0.90?"}
        Step5["RegisterModel (IrisClassificationModels Registry)"]

        Step1 --> Step2 --> Step3 --> Step4
        Step4 -- Yes --> Step5
        Step4 -- No --> Reject["Halt Pipeline Execution"]
    end

    subgraph Serving ["5. Model Deployment"]
        EC2["EC2 Instance (FastAPI Port 80)"]
        SMEndpoint["SageMaker Real-Time REST Endpoint"]
        Step5 -.-> SMEndpoint
    end

    subgraph Observability ["6. Monitoring & Health"]
        Dashboard["CloudWatch: MLOps-Production-Dashboard"]
        Alarm["CloudWatch Alarm: Lambda Errors"]
        SNS["Amazon SNS: MLOps-Alerts-Topic"]

        Lambda -.-> Dashboard
        Lambda -.-> Alarm
        Alarm --> SNS
    end

    BuildSpec -->|On Pass| Step1
    Lambda -->|Trigger| Step1

