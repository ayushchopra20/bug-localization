# Bug Localization

This project performs information-retrieval based bug localization: given a bug report (summary + description), it ranks source files by textual similarity to help identify which files are likely relevant to fix the bug.

**Notebook:** `bug-localization/main.ipynb`
**Dataset:** `CIS580_Assignment_BugLocalization/AspectJ_Dataset.txt`
**Source files root:** `CIS580_Assignment_BugLocalization/sourceFile_aspectj/org.aspectj`

**Outputs:**
- `bug_localization_ranks.csv`: best (lowest) rank per bug for any matching ground-truth file name
- `bug_localization_topN.csv`: top-N ranked file paths per bug

## What Is Imported
- `os`, `re`: file system traversal and regex cleanup
- `numpy` (`np`): numeric arrays and sorting
- `pandas` (`pd`): tabular data loading and CSV writing
- `sklearn.feature_extraction.text.TfidfVectorizer`: build TF-IDF features from text
- `sklearn.metrics.pairwise.cosine_similarity`: compute cosine similarity scores
- `nltk`: natural language toolkit for tokenization and stemming
	- `nltk.corpus.stopwords`: English stopwords
	- `nltk.stem.PorterStemmer`: stemming tokens
	- `nltk.tokenize.word_tokenize`: tokenization

The notebook downloads NLTK data packages: `punkt` and `stopwords` (and attempts `punkt_tab`).

## Requirements
- Python 3.10+
- Packages (already present in the provided virtual environment `bug_local/`):
	- `numpy`, `pandas`, `scikit-learn`, `nltk`, `scipy`

If running outside the provided environment, install:

```powershell
python -m venv bug_local; .\bug_local\Scripts\Activate.ps1
pip install numpy pandas scikit-learn nltk scipy
python -c "import nltk; nltk.download('punkt'); nltk.download('stopwords')"
```

## Goal
- Preprocess bug reports and Java source files (lowercasing, removing non-alphanumerics, tokenizing, stopword removal, stemming).
- Build a unified TF-IDF matrix over all bug and source texts.
- For each bug, compute cosine similarity against all source files and rank files from most similar to least.
- Produce CSV outputs for evaluation and inspection.

## Expected Results
- `bug_localization_ranks.csv`: For each `BugID`, the best rank achieved among ground-truth file name matches. If no match, rank defaults to `len(file_paths) + 1`.
- `bug_localization_topN.csv`: For each `BugID`, the top `N` file paths by similarity.

Example interpretation:
- A lower rank (e.g., 1–10) indicates higher similarity between the bug report and a source file.
- The top-N list helps navigate candidate files quickly during triage.

## How To Run
1. Open `bug-localization/main.ipynb` in Jupyter.
2. Ensure dataset and source paths exist:
	 - `CIS580_Assignment_BugLocalization/AspectJ_Dataset.txt`
	 - `CIS580_Assignment_BugLocalization/sourceFile_aspectj/org.aspectj`
3. Run all cells. On first run, NLTK will download required resources.
4. Check generated CSVs in the `bug-localization/` folder.

## Notes
- File name matching uses only the base file name from the ground truth when comparing to normalized source paths, to accommodate renamed or relocated files.
- Adjust `TOP_N` in the notebook to change how many top files are exported per bug.