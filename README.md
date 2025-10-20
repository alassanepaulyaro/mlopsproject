# Student Performance Predictor - MLOps Project

[![CI/CD Pipeline](https://github.com/yourusername/mlopsproject/workflows/workflow/badge.svg)](https://github.com/yourusername/mlopsproject/actions)

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Running the Application](#running-the-application)
- [Machine Learning Pipeline](#machine-learning-pipeline)
  - [Data Ingestion](#1-data-ingestion)
  - [Data Transformation](#2-data-transformation)
  - [Model Training](#3-model-training)
  - [Prediction Pipeline](#4-prediction-pipeline)
- [Application Architecture](#application-architecture)
- [CI/CD Pipeline](#cicd-pipeline)
- [Deployment](#deployment)
  - [AWS Elastic Beanstalk](#aws-deployment-option-1-elastic-beanstalk-with-codepipeline)
  - [AWS EC2 with Docker](#aws-deployment-option-2-ec2-with-docker-and-github-actions)
  - [Azure Web App](#azure-deployment-with-container-registry)
- [API Endpoints](#api-endpoints)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This project is a comprehensive **Student Exam Performance Indicator** application that demonstrates end-to-end MLOps practices. It predicts a student's math score based on various demographic and academic factors using machine learning models deployed through automated CI/CD pipelines.

The application showcases:
- Complete ML pipeline from data ingestion to model deployment
- Multiple deployment strategies (AWS and Azure)
- Automated CI/CD with GitHub Actions
- Docker containerization
- Production-ready Flask web application

### Key Capabilities

Users can input the following information to receive math score predictions:
- Gender
- Race/Ethnicity
- Parental level of education
- Lunch type (standard/free or reduced)
- Test preparation course completion
- Reading score
- Writing score

---

## Features

- **End-to-End ML Pipeline**: Automated data ingestion, preprocessing, training, and prediction
- **Multiple Model Comparison**: Evaluates 7 different regression models with hyperparameter tuning
- **Web Interface**: User-friendly Flask application for real-time predictions
- **CI/CD Integration**: Automated testing, building, and deployment with GitHub Actions
- **Multi-Cloud Deployment**: Support for both AWS and Azure platforms
- **Docker Support**: Containerized application for consistent deployment
- **Modular Architecture**: Clean, maintainable code structure following software engineering best practices

---

## Project Structure

```
mlopsproject/
│
├── .ebextensions/              # AWS Elastic Beanstalk configuration
│   └── python.config           # WSGI configuration
│
├── .github/
│   └── workflows/
│       └── main.yaml           # GitHub Actions CI/CD pipeline
│
├── artifacts/                  # Generated artifacts (models, data splits)
│   ├── model.pkl               # Trained model
│   ├── preprocessor.pkl        # Preprocessing pipeline
│   ├── train.csv               # Training dataset
│   └── test.csv                # Test dataset
│
├── notebook/                   # Jupyter notebooks for EDA and experimentation
│   ├── 1 . EDA STUDENT PERFORMANCE .ipynb
│   ├── 2. MODEL TRAINING.ipynb
│   └── data/                   # Raw dataset
│
├── src/
│   ├── components/             # ML pipeline components
│   │   ├── __init__.py
│   │   ├── data_ingestion.py       # Data loading and splitting
│   │   ├── data_transformation.py  # Feature engineering & preprocessing
│   │   └── model_trainer.py        # Model training and evaluation
│   │
│   ├── pipeline/               # Inference and training pipelines
│   │   ├── __init__.py
│   │   ├── predict_pipeline.py     # Prediction logic
│   │   └── train_pipeline.py       # Training orchestration
│   │
│   ├── __init__.py
│   ├── exception.py            # Custom exception handling
│   ├── logger.py               # Logging configuration
│   └── utils.py                # Utility functions (save/load models, etc.)
│
├── templates/                  # Flask HTML templates
│   ├── index.html              # Landing page
│   └── home.html               # Prediction form and results
│
├── app.py                      # Flask application entry point
├── Dockerfile                  # Docker container configuration
├── requirements.txt            # Python dependencies
├── setup.py                    # Package installation setup
├── pyproject.toml              # Project metadata
└── README.md                   # Project documentation
```

---

## Tech Stack

### Machine Learning & Data Science
- **Python 3.8+**
- **scikit-learn** - ML models and preprocessing
- **pandas** - Data manipulation
- **numpy** - Numerical computing
- **XGBoost** - Gradient boosting models
- **CatBoost** - Categorical feature boosting
- **dill** - Object serialization

### Web Framework
- **Flask** - Web application framework

### DevOps & Cloud
- **Docker** - Containerization
- **GitHub Actions** - CI/CD automation
- **AWS ECR** - Container registry
- **AWS Elastic Beanstalk** - Platform as a Service
- **AWS EC2** - Virtual machines
- **Azure Container Registry** - Azure container storage
- **Azure Web Apps** - Azure hosting service

### Visualization
- **matplotlib** - Data visualization
- **seaborn** - Statistical graphics

---

## Getting Started

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)
- conda (recommended) or virtualenv
- Docker (for containerized deployment)
- AWS CLI (for AWS deployment)
- Azure CLI (for Azure deployment)

### Installation

1. **Clone the repository**

```bash
git clone https://github.com/yourusername/mlopsproject.git
cd mlopsproject
```

2. **Create a virtual environment**

Using conda (recommended):
```bash
conda create -p venv python==3.8 -y
conda activate venv/
```

Or using virtualenv:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**

```bash
pip install -r requirements.txt
```

4. **Install the package**

```bash
pip install -e .
```

### Running the Application

#### Local Development

1. **Train the model** (if artifacts don't exist)

```bash
python src/components/data_ingestion.py
```

This will:
- Load the dataset
- Split into train/test sets
- Perform data transformation
- Train and save the best model

2. **Start the Flask application**

```bash
python app.py
```

3. **Access the application**

- Homepage: http://localhost:8080
- Prediction page: http://localhost:8080/predictdata

#### Docker Deployment

1. **Build the Docker image**

```bash
docker build -t student-performance-app .
```

2. **Run the container**

```bash
docker run -p 8080:8080 student-performance-app
```

3. **Access the application at** http://localhost:8080

---

## Machine Learning Pipeline

### 1. Data Ingestion

**File**: [src/components/data_ingestion.py](src/components/data_ingestion.py)

**Classes**:
- `DataIngestionConfig`: Configuration for file paths (train, test, raw data)
- `DataIngestion`: Handles data loading and splitting

**Process**:
1. Reads CSV data from `notebook/data/stud.csv`
2. Splits dataset into 80% training and 20% testing sets
3. Saves splits to `artifacts/train.csv` and `artifacts/test.csv`

### 2. Data Transformation

**File**: [src/components/data_transformation.py](src/components/data_transformation.py)

**Classes**:
- `DataTransformationConfig`: Holds preprocessor file path
- `DataTransformation`: Creates and applies transformation pipelines

**Transformation Pipelines**:

**Numerical Pipeline**:
- Imputes missing values using median strategy
- Scales features using StandardScaler

**Categorical Pipeline**:
- Imputes missing values using most frequent value
- Applies one-hot encoding
- Scales encoded features using StandardScaler (with_mean=False)

**Output**: Combined preprocessor saved as `artifacts/preprocessor.pkl`

### 3. Model Training

**File**: [src/components/model_trainer.py](src/components/model_trainer.py)

**Classes**:
- `ModelTrainerConfig`: Specifies model save path
- `ModelTrainer`: Manages training and evaluation

**Models Evaluated**:
1. Random Forest Regressor
2. Decision Tree Regressor
3. Gradient Boosting Regressor
4. Linear Regression
5. XGBoost Regressor
6. CatBoost Regressor
7. AdaBoost Regressor

**Process**:
- Performs hyperparameter tuning using GridSearchCV
- Evaluates models using R² score on test set
- Selects and saves the best-performing model as `artifacts/model.pkl`

### 4. Prediction Pipeline

**File**: [src/pipeline/predict_pipeline.py](src/pipeline/predict_pipeline.py)

**Classes**:
- `PredictPipeline`: Loads model and preprocessor, performs predictions
- `CustomData`: Converts user input into DataFrame format

**Process**:
1. Load saved model and preprocessor
2. Transform input data using preprocessor
3. Generate prediction
4. Return math score prediction

---

## Application Architecture

### Flask Application Flow

**File**: [app.py](app.py)

1. **User visits** `/` → Renders landing page ([index.html](templates/index.html))
2. **User navigates to** `/predictdata` (GET) → Displays input form ([home.html](templates/home.html))
3. **User submits form** (POST) → Process:
   - Collect form data
   - Create `CustomData` object
   - Convert to DataFrame
   - Load `PredictPipeline`
   - Transform and predict
   - Display result on page

### Exception Handling & Utilities

**File**: [src/exception.py](src/exception.py)
- `CustomException`: Formats and logs error messages with detailed traceback

**File**: [src/logger.py](src/logger.py)
- Configures application-wide logging

**File**: [src/utils.py](src/utils.py)
- `save_object()`: Serializes Python objects to pickle files
- `load_object()`: Deserializes pickle files
- `evaluate_models()`: Evaluates and compares multiple models

---

## CI/CD Pipeline

**File**: [.github/workflows/main.yaml](.github/workflows/main.yaml)

### Workflow Overview

The GitHub Actions workflow consists of three main jobs:

#### 1. Continuous Integration
- Triggers on push to `main` branch (except README changes)
- Runs linting checks
- Executes unit tests

#### 2. Continuous Delivery
- Builds Docker image
- Tags image with `latest`
- Pushes to Amazon ECR

#### 3. Continuous Deployment
- Runs on self-hosted runner (EC2 instance)
- Pulls latest image from ECR
- Deploys container to production
- Exposes application on port 8080
- Cleans up old images and containers

### Required GitHub Secrets

Add these secrets to your GitHub repository (Settings → Secrets and variables → Actions):

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION`
- `AWS_ECR_LOGIN_URI`
- `ECR_REPOSITORY_NAME`

---

## Deployment

### AWS Deployment Option 1: Elastic Beanstalk with CodePipeline

This approach uses AWS managed services for automated deployment.

#### Prerequisites
- AWS Account
- GitHub repository

#### Steps

**1. Create Elastic Beanstalk Application**

1. Navigate to AWS Management Console → Elastic Beanstalk
2. Click "Create Application"
3. Configure:
   - Application name: `studentperformance`
   - Platform: Python
   - Upload your code or use sample application
4. Create environment:
   - Environment name: `studentperformance-env`
   - Domain: (auto-generated or custom)

Reference: [AWS EB Documentation](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environments-create-wizard.html)

**2. Create CodePipeline**

1. Navigate to AWS Management Console → CodePipeline
2. Click "Create pipeline"
3. Configure:
   - Pipeline name: `studentperformancepipeline`
   - Service role: Create new or use existing
4. Add source stage:
   - Source provider: GitHub (Version 2)
   - Connect to GitHub and select repository
   - Branch: `main`
   - Change detection: Use webhooks
5. Skip build stage (or add CodeBuild if needed)
6. Add deploy stage:
   - Deploy provider: AWS Elastic Beanstalk
   - Application name: `studentperformance`
   - Environment name: `studentperformance-env`
7. Review and create pipeline

**Configuration File**: [.ebextensions/python.config](.ebextensions/python.config)
```yaml
option_settings:
  "aws:elasticbeanstalk:container:python":
    WSGIPath: application:application
```

---

### AWS Deployment Option 2: EC2 with Docker and GitHub Actions

This approach provides more control and uses containerization.

#### Step 1: Create IAM User

1. Go to AWS Console → IAM → Users
2. Click "Create user"
3. User name: `mlopsuser`
4. Select "Programmatic access"
5. Attach policies:
   - `AmazonEC2ContainerRegistryFullAccess`
   - `AmazonEC2FullAccess`
6. Save access key ID and secret access key

#### Step 2: Create ECR Repository

1. Navigate to AWS Console → ECR
2. Click "Create repository"
3. Repository name: `studentperformance`
4. Keep other settings as default
5. Save the repository URI (e.g., `123456789.dkr.ecr.us-east-1.amazonaws.com/studentperformance`)

#### Step 3: Launch EC2 Instance

1. Navigate to AWS Console → EC2
2. Click "Launch Instance"
3. Configure:
   - Name: `mlops-app-server`
   - AMI: Ubuntu Server 22.04 LTS
   - Instance type: t2.medium (or t2.micro for testing)
   - Key pair: Create new or select existing
   - Security group: Allow ports 22 (SSH) and 8080 (Application)
4. Launch instance

#### Step 4: Setup Docker on EC2

1. **Connect to EC2 instance**

```bash
ssh -i your-key.pem ubuntu@your-ec2-public-ip
```

2. **Update system (optional but recommended)**

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
```

3. **Install Docker**

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu
newgrp docker
```

4. **Verify installation**

```bash
docker --version
docker ps
```

#### Step 5: Configure GitHub Actions Runner

1. Go to your GitHub repository
2. Navigate to Settings → Actions → Runners
3. Click "New self-hosted runner"
4. Select "Linux" as the OS
5. Run the provided commands on your EC2 instance:

```bash
# Download
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64-2.311.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz
tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz

# Configure
./config.sh --url https://github.com/YOUR_USERNAME/mlopsproject --token YOUR_TOKEN

# Run (as a service)
sudo ./svc.sh install
sudo ./svc.sh start
```

#### Step 6: Add GitHub Secrets

In your GitHub repository, go to Settings → Secrets and variables → Actions:

| Secret Name | Value | Example |
|-------------|-------|---------|
| `AWS_ACCESS_KEY_ID` | From IAM user | `AKIAIOSFODNN7EXAMPLE` |
| `AWS_SECRET_ACCESS_KEY` | From IAM user | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY` |
| `AWS_REGION` | Your AWS region | `us-east-1` |
| `AWS_ECR_LOGIN_URI` | ECR URI without repo name | `123456789.dkr.ecr.us-east-1.amazonaws.com` |
| `ECR_REPOSITORY_NAME` | Repository name | `studentperformance` |

#### Step 7: Deploy

1. Push changes to `main` branch
2. GitHub Actions will automatically:
   - Build Docker image
   - Push to ECR
   - Deploy to EC2
3. Access application at `http://your-ec2-public-ip:8080`

---

### Azure Deployment with Container Registry

Deploy the application to Azure using Container Registry and Web Apps.

#### Step 1: Create Azure Container Registry

1. **Create Container Registry**

```bash
az acr create --resource-group myResourceGroup \
  --name mlopsprojectdocker --sku Basic
```

Or via Azure Portal:
- Navigate to "Create a resource" → Container Registry
- Name: `mlopsprojectdocker`
- Resource group: Create new or use existing
- Location: Choose your region
- SKU: Basic or Standard

2. **Enable Admin Access**

```bash
az acr update -n mlopsprojectdocker --admin-enabled true
```

Or via Portal:
- Go to your Container Registry → Settings → Access keys
- Enable "Admin user"
- Copy Login server, Username, and Password

#### Step 2: Build and Push Docker Image

1. **Login to Azure Container Registry**

```bash
az acr login --name mlopsprojectdocker
```

Or using Docker:
```bash
docker login mlopsprojectdocker.azurecr.io
# Username: mlopsprojectdocker
# Password: (from access keys)
```

2. **Build Docker image**

```bash
docker build -t mlopsprojectdocker.azurecr.io/mltest:latest .
```

3. **Push to Azure Container Registry**

```bash
docker push mlopsprojectdocker.azurecr.io/mltest:latest
```

#### Step 3: Create Azure Web App

1. **Using Azure Portal**

- Navigate to "Create a resource" → Web App
- Configure:
  - Name: `mlopsproject`
  - Publish: **Container**
  - Operating System: Linux
  - Region: Your preferred region
  - App Service Plan: Create new or use existing

- In Container settings:
  - Image Source: Azure Container Registry
  - Registry: `mlopsprojectdocker`
  - Image: `mltest`
  - Tag: `latest`

- Click "Review + Create"

2. **Using Azure CLI**

```bash
az webapp create --resource-group myResourceGroup \
  --plan myAppServicePlan --name mlopsproject \
  --deployment-container-image-name mlopsprojectdocker.azurecr.io/mltest:latest
```

#### Step 4: Configure Web App Settings

1. **Set port configuration**

```bash
az webapp config appsettings set --resource-group myResourceGroup \
  --name mlopsproject --settings WEBSITES_PORT=8080
```

2. **Enable continuous deployment (optional)**

- Go to Web App → Deployment Center
- Choose GitHub as source
- Authorize and select repository and branch
- Save configuration

#### Step 5: Access Application

Your application will be available at: `https://mlopsproject.azurewebsites.net`

---

## API Endpoints

### `GET /`
Returns the landing page of the application.

**Response**: HTML page (index.html)

---

### `GET /predictdata`
Displays the prediction form.

**Response**: HTML form (home.html)

---

### `POST /predictdata`
Accepts student information and returns math score prediction.

**Request Body** (form-data):
```
gender: male/female
ethnicity: group A/B/C/D/E
parental_level_of_education: some high school/high school/some college/associate's degree/bachelor's degree/master's degree
lunch: standard/free or reduced
test_preparation_course: none/completed
reading_score: 0-100
writing_score: 0-100
```

**Response**: HTML page with prediction result

**Example**:
```
Input:
- Gender: Female
- Ethnicity: Group B
- Parental Education: Bachelor's degree
- Lunch: Standard
- Test Prep: Completed
- Reading Score: 72
- Writing Score: 74

Output:
Predicted Math Score: 68.5
```

---

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## License

This project is licensed under the MIT License - see the LICENSE file for details.


---

## Acknowledgments

- Dataset: Student Performance Dataset
- Inspiration: MLOps best practices and end-to-end ML deployment
- Cloud Platforms: AWS and Azure for hosting capabilities
