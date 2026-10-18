# Student Success Analytics Platform

**Live demo:** [student-sucess-predictor-1.onrender.com](https://student-sucess-predictor-1.onrender.com/) (free tier: the first load can take a minute)

A Flask machine learning web app that predicts whether a student is likely to pass based on academic, demographic, and school-related features from the UCI Student Performance dataset.

## Project Goal

This project demonstrates an end-to-end data science workflow:

1. Find a public dataset
2. Clean and analyze the data
3. Train and evaluate machine learning models
4. Build a Flask web application
5. Prepare the project for Render deployment

## Dataset

Dataset: UCI Machine Learning Repository Student Performance Dataset  
Source: https://archive.ics.uci.edu/dataset/320/student%2Bperformance

The dataset contains student achievement data from two Portuguese secondary schools, including grades, demographic information, social factors, and school-related features. The copy used here (`student-por.csv`) has 220 rows and 13 columns.

## Model

The deployed app trains a **Random Forest classifier** (100 trees, 80/20 train/test split, `random_state=42`) when it is built (`python train_model.py`) and serves it with Flask.

Target variable:

- `pass_status = 1` if final grade `G3 >= 10`
- `pass_status = 0` if final grade `G3 < 10`

### Results

Measured by running the same code and split as `train_model.py` on `student-por.csv` (220 students, 44 in the test set):

| Metric | Value |
|---|---|
| Accuracy | 0.91 |
| ROC-AUC | 0.96 |
| Majority-class baseline accuracy | 0.64 |

**Caveats**

- The test set is only 44 students, so these figures are rough estimates.
- The model relies on the first- and second-period grades (`G1`, `G2`). Retraining without them gives about 0.61 accuracy (ROC-AUC 0.57), no better than the baseline, so the model is only useful once period grades exist, not as an early warning.
- `models/training_summary.txt` and `models/model_metrics.csv` come from an earlier version of `train_model.py` that compared Logistic Regression, Random Forest and Gradient Boosting (best: Gradient Boosting, 0.95 accuracy). The current script does not reproduce them and the deployed app does not use them. [TODO: either restore the comparison script or remove those two files.]

## Features Used in the App

The deployed form uses a simplified set of student-facing inputs:

- Age
- Study time
- Past failures
- Absences
- First period grade
- Second period grade
- School support
- Family support
- Paid tutoring
- Internet access
- Higher education goal
- Romantic relationship status

## Tech Stack

- Python
- Pandas
- NumPy
- Scikit-Learn
- Flask
- HTML/CSS
- Joblib
- Render

## Project Structure

```text
student_success_predictor/
├── app.py
├── train_model.py
├── requirements.txt
├── render.yaml
├── Procfile
├── README.md
├── data/
├── models/
├── static/
│   ├── css/
│   │   └── style.css
│   └── js/
│       └── app.js
└── templates/
    ├── index.html
    ├── predict.html
    ├── dashboard.html
    └── about.html
```

## How to Run Locally

```bash
pip install -r requirements.txt
python train_model.py
python app.py
```

Then open:

```text
http://127.0.0.1:5000
```

## Render Deployment

1. Push this project to GitHub.
2. Create a new Web Service on Render.
3. Connect your GitHub repository.
4. Use these settings:

Build command:

```bash
pip install -r requirements.txt && python train_model.py
```

Start command:

```bash
gunicorn app:app
```
