# USFDA Form 483 Observation Analysis

A Python-based NLP and machine learning pipeline for extracting, cleaning, summarizing, and clustering **USFDA Form 483 inspection observations** to identify recurring pharmaceutical compliance issues.

The project was developed during an internship at **Sun Pharmaceutical Industries Limited** with the goal of reducing the manual effort involved in reviewing lengthy FDA inspection reports and providing a more structured view of recurring observations.

---

## About

### What are Form 483 observations?

When the US Food and Drug Administration (FDA) identifies conditions that may represent violations of regulatory requirements during an inspection, the findings are documented in a **Form FDA 483 — Inspectional Observations**.

These reports can contain lengthy and detailed observations covering areas such as:

* Contamination and aseptic practices
* Equipment cleaning and maintenance
* Quality control procedures
* Documentation and process compliance
* Manufacturing and facility practices

Reviewing large numbers of such observations manually can be time-consuming. This project explores an automated pipeline for extracting the observations, reducing their length through summarization, and grouping similar observations using unsupervised machine learning.

---

## Problem Statement

The project addresses three main challenges:

### 1. Manual Data Collection

Relevant information was distributed across publicly available FDA inspection records and associated PDF documents.

### 2. Long and Unstructured Observations

Form 483 observations can be lengthy and contain redundant or noisy text, making manual review difficult.

### 3. Lack of Categorized Information

Similar compliance issues may appear across different inspections and companies, but identifying these patterns manually is difficult at scale.

---

## Project Goals

The primary objectives were to:

* Automate the extraction of Form 483 inspection data.
* Extract individual observations from inspection PDFs.
* Clean and preprocess extracted text.
* Generate concise summaries of lengthy observations.
* Convert summaries into numerical representations.
* Group similar observations using unsupervised clustering.
* Provide a centralized view of recurring compliance issues.

---

## Solution Overview

The implemented pipeline follows these stages:

```text
                 FDA Inspection Records
                          │
                          ▼
                  Data Collection
                          │
                          ▼
                 PDF Text Extraction
                          │
                          ▼
                Observation Extraction
                          │
                          ▼
                  Text Cleaning
                          │
                          ▼
              BART-based Summarization
                          │
                          ▼
                 TF-IDF Vectorization
                          │
                          ▼
                  K-Means Clustering
                          │
                          ▼
        Recurring Compliance Issue Groups
                          │
                          ▼
              Analysis & Visualization
```

---

## Methodology

### 1. Data Collection

Inspection records were collected from publicly available information provided by the FDA.

The initial dataset contains **1,725 inspection records** with metadata such as:

* FEI Number
* Company Name
* Record Type
* Record Date
* Date Posted
* Document URL
* Record ID

The project then identifies relevant records and extracts information from their associated PDF inspection reports.

---

### 2. PDF Extraction

PDF documents are downloaded programmatically and processed using Python PDF-extraction libraries.

The pipeline extracts text from the inspection documents and identifies sections corresponding to individual observations.

The extracted information is organized into structured records containing fields such as:

```text
Company Name
FEI Number
Record Type
Record ID
Country
Pincode
Observation 1
Observation 2
...
Observation N
```

---

### 3. Text Cleaning

Raw PDF extraction can introduce unwanted formatting, characters, and extraction artifacts.

The notebook performs preprocessing operations including:

* Removal of irrelevant characters
* Text normalization
* Tokenization
* Basic spelling correction
* Removal of noisy extracted content

The cleaned observations are then used as input to the summarization stage.

---

### 4. Text Summarization

Long observations are summarized using the Hugging Face Transformers pipeline with:

```text
facebook/bart-large-cnn
```

BART is used to generate shorter representations of observations while preserving their main information.

Conceptually:

```text
Long Form 483 Observation
            │
            ▼
       BART Model
            │
            ▼
     Concise Summary
```

This reduces the amount of text that needs to be reviewed during downstream analysis.

---

### 5. Text Vectorization

The generated summaries are converted into numerical representations using **TF-IDF (Term Frequency–Inverse Document Frequency)**.

```python
TfidfVectorizer(stop_words="english")
```

This transforms the textual summaries into feature vectors that can be processed by machine learning algorithms.

---

### 6. K-Means Clustering

The TF-IDF feature vectors are passed to a **K-Means clustering** model.

The implementation uses:

```text
Number of clusters = 5
```

The objective is to group summaries containing similar themes and terminology.

Conceptually:

```text
                    Summarized Observations
                              │
                              ▼
                        TF-IDF Vectors
                              │
                              ▼
                        K-Means (k=5)
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
       Cluster 0           Cluster 1           Cluster 2
          │                   │                   │
          └────────────── Similar Issues ─────────┘
```

The resulting clusters can then be inspected to understand recurring categories of observations.

---

## Results

The clustering stage produced **5 groups of similar summarized observations**.

The resulting data contains:

```text
Summary
Company_name
Cluster
```

The repository includes the generated clustered dataset in:

```text
csv files/Summary_cluster.csv
```

The analysis was also visualized using word clouds to inspect the dominant terms associated with individual clusters.

Example analytical questions enabled by the output include:

* What types of compliance observations occur repeatedly?
* Which issues appear across multiple pharmaceutical companies?
* What themes dominate a particular cluster?
* What observations are associated with a specific company?

---

## Example Pipeline Output

A simplified representation of the generated data is:

```text
┌─────────────────────────────────────────────────────┐
│ Summary                                             │
│ "Equipment and utensils are not cleaned..."         │
├─────────────────────────────────────────────────────┤
│ Company                                             │
│ Sun Pharmaceutical Industries Limited              │
├─────────────────────────────────────────────────────┤
│ Cluster                                             │
│ Cluster 2                                           │
└─────────────────────────────────────────────────────┘
```

This allows observations to be analyzed by both **company** and **cluster**.

---

## Repository Structure

```text
Sunpharma-internship_project/
│
├── Sunpharma_proj.ipynb
│       Main analysis notebook containing the complete
│       extraction, preprocessing, summarization,
│       vectorization, clustering, and analysis workflow.
│
├── Report.pptx
│       Internship project presentation.
│
├── Internship_Certificate_Sunpharma.pdf
│       Internship completion certificate.
│
├── README.md
│       Project documentation.
│
└── csv files/
    ├── sample_data.csv
    ├── observations.csv
    ├── cleaned_obs.csv
    ├── summarized_file.csv
    └── Summary_cluster.csv
```

---

## Dataset Files

### `sample_data.csv`

Contains the initial inspection-record metadata collected for the analysis.

### `observations.csv`

Contains extracted Form 483 observations organized into structured columns.

### `cleaned_obs.csv`

Contains preprocessed observation text after cleaning operations.

### `summarized_file.csv`

Contains the generated summaries produced using the BART summarization model.

### `Summary_cluster.csv`

Contains summarized observations along with their assigned K-Means cluster:

```text
Summary | Company_name | Cluster
```

---

## Technology Stack

| Category                | Technology                |
| ----------------------- | ------------------------- |
| Language                | Python                    |
| Data Processing         | Pandas                    |
| PDF Processing          | PyPDF2                    |
| HTTP Requests           | Requests                  |
| NLP Preprocessing       | NLTK, TextBlob            |
| Text Summarization      | Hugging Face Transformers |
| Summarization Model     | `facebook/bart-large-cnn` |
| Feature Extraction      | TF-IDF                    |
| Machine Learning        | Scikit-learn / PyCaret    |
| Clustering              | K-Means                   |
| Visualization           | Matplotlib, WordCloud     |
| Development Environment | Jupyter Notebook          |

---

## Getting Started

### Prerequisites

The project is implemented as a Jupyter Notebook.

Recommended environment:

```text
Python 3.x
Jupyter Notebook / JupyterLab
```

Install the primary dependencies:

```bash
pip install pandas
pip install requests
pip install PyPDF2
pip install nltk
pip install textblob
pip install transformers
pip install torch
pip install scikit-learn
pip install pycaret
pip install wordcloud
pip install matplotlib
```

Depending on the local environment, additional NLTK resources may need to be downloaded.

---

## Running the Project

Start Jupyter:

```bash
jupyter notebook
```

Open:

```text
Sunpharma_proj.ipynb
```

Then execute the notebook sequentially.

The notebook is organized around the following workflow:

```text
1. Load inspection metadata
2. Clean the metadata
3. Identify relevant records
4. Download inspection PDFs
5. Extract observations
6. Clean extracted text
7. Generate BART summaries
8. Create TF-IDF vectors
9. Perform K-Means clustering
10. Inspect clusters
11. Generate visualizations
12. Save processed datasets
```

---

## Example Machine Learning Workflow

### TF-IDF

```python
vectorizer = TfidfVectorizer(stop_words="english")
X = vectorizer.fit_transform(data["Summary"]).toarray()
```

### K-Means

```python
kmeans = create_model(
    "kmeans",
    num_clusters=5
)
```

The trained model is then used to assign each summarized observation to a cluster.

---

## Challenges

One of the main challenges encountered during the project was **PDF text extraction quality**.

FDA inspection PDFs may contain highlighted sections, unusual formatting, and document structures that are not represented cleanly in extracted text.

This can result in artifacts such as:

```text
incorrect spacing
missing characters
fragmented words
unexpected tokens
```

These extraction issues can subsequently affect:

```text
Text Cleaning
      ↓
Summarization
      ↓
TF-IDF Features
      ↓
Clustering Quality
```

Therefore, document preprocessing is an important component of the overall pipeline.

---

## Limitations

This project is primarily an exploratory NLP and unsupervised-learning workflow rather than a production-ready regulatory compliance system.

Some limitations include:

* PDF extraction quality varies between documents.
* Text-cleaning rules may not handle every formatting pattern.
* BART-generated summaries depend on the quality of extracted text.
* The current workflow requires human inspection to interpret and label the resulting clusters.

These limitations provide opportunities for further improvement.

---

## Future Improvements

The project can be extended into a more robust analytical application by:

### Improved Document Processing

Use more reliable document parsing or OCR techniques to handle difficult PDF layouts and formatting.

### Better Summarization

Compare multiple transformer-based summarization approaches and evaluate summaries using domain-specific criteria.

### Improved Clustering

Experiment with additional unsupervised approaches and compare cluster quality.

### Human-in-the-Loop Classification

Allow subject-matter experts to label clusters and use those labels to create supervised models.

### Interactive Application

Build an application that allows users to:

* Search inspection records
* Filter by company
* Explore clusters
* Read original observations
* Compare summaries
* Review recurring compliance themes

### Model Evaluation

Introduce quantitative evaluation for:

* Summary quality
* Cluster separation
* Category consistency
* Agreement with human/SME classification

---

## Key Takeaways

The project demonstrates an end-to-end workflow combining **data acquisition, document processing, NLP, transformer-based summarization, feature engineering, unsupervised machine learning, and visualization**.

The key learning areas include:

* Extracting structured information from semi-structured documents
* Cleaning noisy text generated through PDF extraction
* Applying pretrained transformer models for summarization
* Converting text into machine-learning features using TF-IDF
* Applying K-Means clustering to discover recurring patterns
* Interpreting and visualizing unsupervised learning results

---

## Project Context

This project was developed as part of an **AI internship at Sun Pharmaceutical Industries Limited**.

The work focused on applying Python, NLP, and machine learning techniques to automate the analysis of pharmaceutical inspection observations and surface recurring compliance themes.

---

## Author

**Satyadev Subudhi**

Computer Science Engineering — IIT Palakkad

[GitHub](https://github.com/Satyadev-Subudhi)

[LinkedIn](https://www.linkedin.com/in/satyadev-subudhi-365325244/)
