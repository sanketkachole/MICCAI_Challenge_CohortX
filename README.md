# CohortX Task 1 - System C (Sanket Kachole)

Extraction of six cohort-selection fields (conditions, study type, sex, minimum
age, maximum age, eligibility criteria) from PubMed Central NXML articles for the
MICCAI 2026 CohortX Challenge, Task 1.

Uses **only the provided competition data** - no external datasets, no trial-
registry lookup, no network APIs at inference. Runs offline on CPU.

## Method (summary)

- **Eligibility criteria** - supervised passage ranking. Articles are parsed into
  sections; candidate passages are generated and filtered, encoded with a frozen
  BioBERT sentence encoder, and scored by a gradient-boosting regressor trained on
  the 416 training articles to predict FM3S similarity to the reference. Top-ranked
  passages are assembled under inclusion/exclusion headings.
- **Conditions** - training-derived disease vocabulary matched in title/abstract/
  keywords, with a generic fallback.
- **Study type / sex / age** - high-precision rules; data-driven age defaults
  (18 years / "Not Specified") with result-statistic, case-report, and animal-age
  suppression.

No neural network is trained. BioBERT is used frozen (embeddings only); the only
trained component is the gradient-boosting ranker, rebuilt from training data each
run. Full details are in the accompanying CohortX Task 1 paper (System C).

## Installation

1. Clone this repository:

       git clone https://github.com/<your-username>/MICCAI_CohortX.git
       cd MICCAI_CohortX

2. Create and activate a Python 3.10+ environment (example with conda):

       conda create -n cohortx python=3.10 -y
       conda activate cohortx

3. Install dependencies:

       pip install -r requirements.txt
       python -m spacy download en_core_web_sm

   NLTK WordNet data downloads automatically on first run.

## Data

Place the competition data at:

    /kaggle/input/competitions/cohort-x-task-1

containing `Task_1.xlsx` and the `PMC_NXML_Archives` folder. The project was
built in the Kaggle notebook environment with this dataset attached. To run
elsewhere, use the same path or edit the paths near the top of the notebook.

## Model

The BioBERT encoder `pritamdeka/BioBERT-mnli-snli-scinli-scitail-mednli-stsb`
downloads automatically from Hugging Face on first run and is then cached.
Internet is required only for this one-time download; all inference runs offline
on CPU thereafter.

## Usage

Open `cohortx_task1_submission.ipynb` and run all cells top to bottom. It:

1. loads and parses the NXML articles,
2. trains the eligibility ranker on the training articles,
3. selects settings on a held-out split,
4. predicts the test set and writes `submission.csv`.

## Environment

Developed on the Kaggle CPU environment (Python 3.10+, 16 GB RAM, no GPU).
Package versions follow the Kaggle base image; `requirements.txt` lists the
libraries used.

## Author

Sanket Kachole — Division of Computational Pathology, Indiana University School
of Medicine, Indianapolis, IN, USA.
