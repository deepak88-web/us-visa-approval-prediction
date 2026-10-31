# US Visa Approval Prediction

A machine learning project that predicts whether a US employment-based visa application will be certified or denied, based on applicant and employer information. It includes a data and model training pipeline and a small web application for predictions.

## What the project does

- Reads visa application records from MongoDB and splits them into training and test data.
- Checks the data schema and reports data drift with Evidently.
- Prepares categorical and numerical features, trains candidate classifiers, and evaluates a new model against the current model using F1 score.
- Stores an accepted model in Amazon S3 and uses it to make predictions through a FastAPI web form.

## Tech stack

Python, pandas, scikit-learn, imbalanced-learn, XGBoost, CatBoost, FastAPI, MongoDB, Amazon S3, and Evidently.

## Project structure

```text
config/                 Data schema and model search configuration
notebook/               Exploratory analysis and model development notebooks
static/                 Web application styles
templates/              Prediction form template
us_visa/components/     Ingestion, validation, transformation, training, evaluation, and model storage
us_visa/pipeline/       Training and prediction pipeline orchestration
app.py                  FastAPI application
```

## Setup

Requires Python 3.13.1, access to the configured MongoDB database, and AWS credentials with access to the model bucket.

1. Create and activate a virtual environment:

   ```powershell
   python -m venv .venv
   .venv\Scripts\Activate.ps1
   ```

2. Install dependencies:

   ```powershell
   pip install -r requirements.txt
   ```

3. Create a `.env` file in the project root and set the MongoDB connection string:

   ```env
   MONGODB_URL_KEY=<your-mongodb-connection-string>
   ```

4. Set AWS credentials in your environment. The application reads `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`; the configured AWS region is `ap-southeast-2`. The model bucket is configured in `us_visa/constants/__init__.py`.

Keep credentials in environment variables or a local `.env` file. Do not commit secrets.

## Run the application

Start the FastAPI app from the project root:

```powershell
uvicorn app:app --host 0.0.0.0 --port 8080
```

Open `http://localhost:8080` to use the prediction form. The form accepts applicant and employer details and displays the model's predicted outcome. The `/train` route starts a training run and requires the MongoDB and AWS configuration above.

## Training pipeline

The training pipeline runs these stages in order:

1. Ingest records from MongoDB and create a train/test split.
2. Validate the expected columns and generate a data drift report.
3. Transform features and address class imbalance in the training data.
4. Train and evaluate candidate models using F1 score.
5. Upload the accepted model to Amazon S3.

Pipeline artifacts, including the drift report, are written under `artifact/` for each run.

## CI/CD Deployment

CI/CD deployment has been configured using **GitHub Actions, Docker, Amazon ECR, and Amazon EC2**. The workflow automatically builds the Docker image, pushes it to the configured Amazon ECR repository, and deploys the application on the EC2 instance.

The required AWS resources and GitHub Actions secrets have been configured using credentials and resource identifiers from the project's AWS account.


ecr repo uri : 107489387220.dkr.ecr.ap-southeast-2.amazonaws.com/visa
