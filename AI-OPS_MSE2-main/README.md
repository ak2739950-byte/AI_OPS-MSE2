# AI Grammar Checker — monitoring and retraining practical

This standalone module detects English subject–verb agreement errors. It uses a demonstration champion trained for this exercise and 72 authored examples. The existing application's deployed model and original training records were unavailable. To demonstrate an extension of your own project, connect the module to your app and use your own labelled data as required by the assessment.

## Run the demonstration

Extract the ZIP, enter the Grammar_MLOps folder and run with Python 3.11 or 3.12:

    python -m venv .venv
    source .venv/bin/activate
    # On Windows PowerShell use .venv\Scripts\Activate.ps1 instead.
    python -m pip install -r requirements.txt
    python workflow.py

The supplied champion is ready to load. For a new project folder without a champion, use python workflow.py --init-demo to initialize one from original.csv. Each run saves its own evaluation.json, predictions.csv and candidate.joblib under runs/. execution.txt records the packaged run.

## Assessment demonstration

1. **Prepare data.** Open the CSV files. Label 1 means a grammatical error and 0 means correct. Show the terminal checks for required columns, missing or empty values, valid labels, duplicate sentences, cross-split overlap, dataset size and label frequencies.
2. **Evaluate the champion.** On 24 incoming examples, the saved model scored 0.50 in accuracy, precision, recall and F1. Monitoring happens before those examples enter training.
3. **Retrain.** Clone the word n-gram vectorizer and logistic regression pipeline, fit it on the 32 original and 24 incoming sentences, and save the candidate. The vocabulary is fitted only on training data.
4. **Apply the quality gate.** Open evaluation.json. On the same 16 held-out examples, champion F1 was 0.875 and candidate F1 was 1.000. The rule requires strictly higher F1. This run marked the candidate eligible for promotion. A tie or decrease retains the champion. Promotion requires a separate deployment decision.
5. **Explain a prediction.** Open predictions.csv and locate a row where the champion and candidate predictions differ. Compare both with the human-authored label. Use execution.txt to show the full run.

## Viva

**Why F1?** Missing an error leaves incorrect text unflagged. Flagging a correct sentence produces an unnecessary correction. Error-class F1 accounts for both through recall and precision.

**Why a separate holdout?** The candidate trains on the incoming batch. Testing it on those same sentences would reward memorization. Both models therefore face the same reserved examples.

**What does 1.0 mean here?** All 16 test examples were classified correctly. The examples share templates and cover a narrow English grammar task. This experiment does not measure general writing quality, correction quality or Hinglish performance.

**How can my app use this?** For a Python app, load champion.joblib once with joblib.load and call model.predict([sentence])[0] for a sentence-level error flag. Keep the correction engine responsible for producing corrected text. Supply original, incoming and holdout CSVs in the same format for project-specific evaluation.


The packaged run files `evaluation.json`, `predictions.csv` and `candidate.joblib` are included at the repository root. New executions create timestamped folders under `runs/`.
