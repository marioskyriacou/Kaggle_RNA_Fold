# Kaggle_RNA_Fold
Solve RNA structure prediction, one of biology's remaining grand challenges
# 🧬 RNA 3D Structure Prediction with Machine Learning

Predicting Ribonucleic Acid (RNA)’s 3D structure involves using only its sequence to figure out how it folds into the structures that define its function.In this competition, we develop machine learning models to predict an RNA molecule’s 3D structure from its sequence. The goal is to improve our understanding of biological processes and drive new advancements in medicine and biotechnology.

Submissions are scored using **TM-score ("template modeling" score)**, which ranges from **0.0 to 1.0** — with 1.0 indicating a perfect match between predicted and experimental structures.

---

## 📂 Dataset Description

### 📄 `[train/validation/test]_sequences.csv`

Contains the RNA sequences and metadata:

- **target_id**: Unique identifier (formatted as `pdb_id_chain_id` in train set)
- **sequence**: RNA sequence (A, C, G, U; other characters may appear in some train samples)
- **temporal_cutoff**: Date of sequence publication
- **description**: Origin or additional info of the molecule
- **all_sequences**: FASTA-formatted sequence including potential additional chains

### 📄 `[train/validation]_labels.csv`

Contains experimental 3D structure labels for training and validation:

- **ID**: Combination of `target_id` and residue number
- **resname**: RNA base (A, C, G, U)
- **resid**: Residue index (1-based)
- **x_1, y_1, z_1, ...**: Coordinates (Angstroms) of C1' atom; multiple structures may exist (e.g., `x_2`, `y_2`, `z_2`, etc.)

---

## 🧠 Modeling Approach

Our approach treats the RNA sequence structure prediction as a **multi-output regression** problem, where the goal is to predict 3D coordinates `(x, y, z)` of each nucleotide's C1' atom.

### 🧩 Feature Engineering

We encoded each RNA sequence into a structured format with the following columns:

- `ID`: Sample identifier
- `seq_len`: Sequence length
- One-hot encoded bases: `A`, `U`, `G`, `C`

These are used as input features for training.

### 🔁 Regression Chain Ensemble

We trained **5 different ML regression models** to predict the 3D structure:

- **Model 1:** `XGBRegressor`
- **Model 2:** `LGBMRegressor`
- **Model 3:** `GradientBoostingRegressor`
- **Model 4:** `RandomForestRegressor`
- **Model 5:** `KNeighborsRegressor`

Each model is wrapped in a **regression chain** setup, where the predicted outputs `(x, y, z)` are sequentially predicted as chained targets to capture correlations between coordinates.

Each model was trained independently using the same dataset to improve robustness and reduce overfitting via ensemble averaging.

---

## 🧪 Evaluation

- Predictions are compared to ground truth using **TM-score**, which evaluates the similarity between predicted and actual 3D structures.
- In some cases, multiple reference structures are used to validate the prediction quality.

---

## 📌 Notes

- For targets with multiple experimental conformations, each reference structure is considered valid for scoring.
- Models were trained to generalize across potential noise or variant forms in the training data.
- The validation strategy includes robust handling of sequences with ligands or additional molecular chains.

---




