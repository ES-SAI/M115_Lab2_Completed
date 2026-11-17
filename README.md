Static Malware Detection with Machine Learning
Project Overview
This project is a completed practical lab for Module M115: AI &
Intelligent Systems in the Master's programme in Intelligent Systems
and Cyber Defense at ENSIASD Taroudant, Ibn Zohr University.

The project develops and evaluates a machine-learning workflow for
static malware detection in Windows Portable Executable (PE) files.
Its purpose is to support a Security Operations Center (SOC) by
identifying suspicious files before execution and prioritizing them for
analyst review.

The work is framed around AtlasPay Services, a fictional Moroccan
payment-services company. The scenario describes a suspicious
software-update incident and operational constraints for a small SOC.
Important: The notebook generates a deterministic, EMBER-inspired
synthetic dataset containing 6,000 PE-file records. It is not the real
EMBER benchmark, AtlasPay production telemetry, or evidence of
real-world detection performance. AtlasPay and the incident are
fictional teaching-scenario elements.

Project Objectives
- Perform a forensic audit of the PE dataset and identify data-quality
  issues.
- Prevent target leakage by excluding information unavailable at
  initial file inspection.
- Build leakage-safe preprocessing and model-evaluation pipelines.
- Compare several machine-learning algorithms using stratified
  cross-validation.
- Investigate model disagreements and explain predictions with
  TreeSHAP.
- Select operating thresholds using development data and evaluate them
  on a separate lockbox test set.
- Compare the costs of analyst-assisted triage and a possible future
  automatic-blocking scenario.
- Formulate a cautious deployment recommendation for the SOC.
Dataset
The notebook creates the dataset during execution; no external dataset
download is required for the supplied workflow.
- Number of records: 6,000
- Target column: Is_Malware
  - 1: Malware
  - 0: Benign
- Prediction unit: One Windows PE file at first inspection.
- Dataset type: Deterministic synthetic, EMBER-inspired teaching
  data.
The forensic audit includes missing values, duplicate records, class
balance, summary statistics, and investigation of a suspicious parser
sentinel value (99999.0) in Virtual_to_Raw_Size_Ratio.

The following columns are excluded from model inputs: -
SHA256_File_ID: an identifier rather than a transferable PE
characteristic. - VT_PostScan_Detections: a post-scan field that would
introduce target leakage.

The sentinel value 99999.0 is treated as missing and handled through
imputation within the model pipeline.
Methodology
1. Data audit and feature preparation
The notebook examines the dataset structure, class distribution, missing
values, duplicate rows, summary statistics, suspicious parser values,
and selected feature relationships.
2. Evaluation protocol
- Stratified 80/20 split into development and lockbox test sets.
- Five-fold stratified cross-validation on the development set.
- Median imputation inside each pipeline to avoid preprocessing
  leakage.
- RobustScaler is used for Logistic Regression; tree-based models do
  not require feature scaling.
- The lockbox test set is reserved for final evaluation and is not
  used for model selection or threshold tuning.
3. Models compared
The notebook compares the following model families:
- Logistic Regression
- Decision Tree
- Random Forest
- LightGBM, when installed; otherwise HistGradientBoostingClassifier
  is used and identified as the fallback
- Stacking classifier
Evaluation includes training and validation F1, validation PR-AUC,
fitting time, and the train-validation F1 gap. Prediction latency is
also measured separately from training time.
4. Error analysis and explainability
The notebook investigates a verified disagreement between candidate
models on a held-out observation. It reports the observed feature
values, model probabilities, predictions, and ground-truth label.

TreeSHAP is used for global and local explanations of the selected
tree-based model. SHAP values describe how features contribute to the
model output; they are not causal evidence and do not independently
prove that a file is malicious.
5. SOC cost and threshold analysis
The fictional teaching scenario provides these illustrative planning
costs:

  Error type                                              Assumed cost
  False positive during analyst triage                            $25
False negative (missed malicious file)                         $500
False positive in a future inline-blocking scenario          $2,000

Thresholds are selected using out-of-fold development predictions, then
frozen before evaluation on the lockbox test set. The notebook compares
analyst-assisted triage with the higher-cost blocking scenario.

The scenario also specifies: - Approximately 12,000 PE-file submissions
per day. - Six SOC analysts. - A target of no more than 120
model-generated review alerts per day.

A test-set alert count is only an indicative exercise; it is not a
validated daily production forecast.
Technologies and Libraries
The notebook uses Python and the following main libraries:
- NumPy
- pandas
- Matplotlib
- scikit-learn
- SHAP
- LightGBM (optional; the notebook uses scikit-learn's
  HistGradientBoosting fallback if LightGBM is unavailable)
Requirements
Recommended: Python 3.10 or a compatible version supported by the
installed library releases, plus Jupyter Notebook or JupyterLab.

Install the main dependencies:
python -m pip install numpy pandas matplotlib scikit-learn shap jupyter
Optional LightGBM installation:
python -m pip install lightgbm
If LightGBM is not installed, the notebook automatically uses
HistGradientBoostingClassifier as the boosting alternative.
How to Run
1. Clone or download this project repository.
2. Open a terminal in the project directory.
3. Install the dependencies listed above.
4. Start Jupyter:
   jupyter notebook
5. Open M115_Lab2_Completed(2).ipynb.
6. Run the notebook cells in order, from top to bottom.
7. Keep the random seed unchanged if you want results comparable with
   the supplied notebook run.
The dataset is generated by the notebook, so the workflow does not
require a separate dataset file.
Expected Outputs
Running the notebook produces:
- Dataset audit statistics and descriptive summaries.
- Feature visualizations.
- A model-comparison table based on five-fold cross-validation.
- Training-time and inference-latency measurements.
- A verified model-disagreement case.
- Global and local SHAP explanations.
- Development-selected thresholds and lockbox test cost metrics.
- A written interpretation and an executive recommendation to the
  fictional AtlasPay CISO.
Exact numerical results depend on the execution environment and
installed optional libraries.
Main Recommendation
The notebook's executive memo recommends beginning with shadow mode,
followed by analyst-assisted triage. It does not recommend
autonomous blocking based on this synthetic experiment alone.

Before considering a blocking pilot, the project identifies the need for
representative time-separated production validation, probability
calibration, drift monitoring, real alert-volume measurement,
independent sandbox or behavioral corroboration, rollback procedures,
human review for business-critical files, and end-to-end load testing.
Limitations
- The dataset is synthetic and cannot establish real-world
  malware-detection performance.
- The fictional class prevalence may differ substantially from a real
  organization's incoming PE-file population.
- Illustrative error costs are assumptions, not measured financial
  losses.
- Static PE features do not reveal all runtime behavior.
- Model-only prediction latency excludes parsing, file I/O,
  integrations, queuing, and analyst handling.
- SHAP explanations describe model behavior but do not establish
  causality.
- The 120-alert daily target cannot be validated from a simple lockbox
  test-set count.
