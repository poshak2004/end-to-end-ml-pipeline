# End-to-End ML Pipeline: Student Exam Score Predictor

A compact regression project that goes all the way from raw CSV to a deployed app: **preprocess → train and select a model → serve in Streamlit**, including single-student and batch CSV predictions.

## Results

600 students, 80/20 split (120 in the test set). Two candidate models share the same preprocessing; the best R² is saved.

| Model | MAE | RMSE | R² |
|---|---|---|---|
| **Ridge Regression** ✅ | **4.25** | **5.36** | **0.768** |
| Random Forest (300 trees) | 5.08 | 6.36 | 0.674 |

The linear model beats the forest. The relationships in this data are mostly additive, and with only 480 training rows the forest overfits. Always benchmark against a simple baseline.

## Pipeline

```
data/raw/student_performance.csv
  → src/preprocess.py   80/20 split → data/processed/{train,test}.csv
  → src/train.py        ColumnTransformer(StandardScaler + OneHotEncoder) + model
                        Ridge vs RandomForest → best by R² → models/model.pkl, metrics.json
  → app.py              Streamlit: single prediction form + batch CSV → downloadable predictions
  → src/viz.py          target distribution → reports/figures/
```

Preprocessing and the model are saved together as one scikit-learn `Pipeline`, so the app passes raw feature values with no separate scaler to keep in sync.

**Features:** study hours, attendance %, sleep hours, distraction hours, past grade, parent education, internet access, part-time job, test prep. **Target:** `exam_score` (0–100).

## Run it

```bash
pip install -r requirements.txt
python src/preprocess.py
python src/train.py
streamlit run app.py
```
