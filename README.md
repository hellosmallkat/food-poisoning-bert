# Foodborne Outbreak Severity Predictor

[![Hugging Face Space](https://img.shields.io/badge/%F0%9F%A4%97%20Live%20Demo-FF9D00?style=for-the-badge&logo=huggingface&logoColor=white)](https://kat4everokay-food-poisoning-bert.hf.space)
[![GitHub Repository](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/hellosmallkat/food-poisoning-bert)
[![Open In Colab](https://img.shields.io/badge/Colab-F9AB00?style=for-the-badge&logo=googlecolab&color=525252&logoColor=white)](https://colab.research.google.com/github/hellosmallkat/food-poisoning-bert/blob/main/Food_poisoning_outbreak_BERT_.ipynb)

A complete pipeline for foodborne outbreak severity prediction, combining **PubMedBERT fine-tuning** with **CDC outbreak data** from 1998-2024.

## Table of Contents
- [Overview](#-overview)
- [Live Demo](#-live-demo)
- [Project Structure](#-project-structure)
- [How It Works](#-how-it-works)
- [Training the Model](#-training-the-model)
- [Running Locally](#-running-locally)
- [Deployment](#-deployment)
- [Data Sources](#-data-sources)
- [Future Improvements](#-future-improvements)

## Overview

This project consists of two main components:

1. **Training Pipeline** (`Food_poisoning_outbreak_BERT_.ipynb`): A comprehensive notebook that:
   - Loads CDC foodborne outbreak data
   - Prepares text descriptions and severity labels
   - Fine-tunes PubMedBERT for binary severity classification
   - Trains an XGBoost forecaster using BERT embeddings

2. **Interactive Web App** (`app.py`): A Gradio application that:
   - Provides a user-friendly interface for outbreak analysis
   - Includes dropdown menus for CDC-validated pathogens
   - Validates inputs (deaths ≤ sick people, illnesses ≤ state population)
   - Displays severity scores with CDC historical context

## Live Demo

**Try it yourself:** [https://kat4everokay-food-poisoning-bert.hf.space](https://kat4everokay-food-poisoning-bert.hf.space)

The live app is hosted on Hugging Face Spaces and embedded in a custom GitHub Pages site at `https://hellosmallkat.github.io/food-poisoning-bert/`.

##  Project Structure
food-poisoning-bert/
├── Food_poisoning_outbreak_BERT_.ipynb # Training notebook (Google Colab)
├── app.py # Gradio web application
├── requirements.txt # Python dependencies
├── index.html # Custom landing page for GitHub Pages
├── README.md # This file
└── fine_tuned_model/ # Saved model (optional, for local use)

text

## 🔬 How It Works

### Severity Classification

The model predicts outbreak severity based on:

1. **Disease baseline severity** (from CDC FoodNet data)
2. **Outbreak scale** (number of illnesses)
3. **Hospitalization rate** (compared to historical averages)
4. **Fatality rate** (weighted most heavily)
5. **State risk modifier** (based on CDC historical data)

### Severity Levels

| Score | Level | Action Required |
|-------|-------|-----------------|
| 7-10 | CRITICAL | Immediate CDC notification, emergency response |
| 5-6 | SEVERE | State health department within 24 hours |
| 3-4 | MODERATE | Local health department notification |
| 0-2 | MILD | Standard reporting, monitor situation |

## Training the Model

The training notebook (`Food_poisoning_outbreak_BERT_.ipynb`) performs the following steps:

### 1. Data Loading & Preparation

# Loads CDC outbreak data from CSV
df = pd.read_csv('outbreaks.csv')

# Creates text descriptions
df['text'] = df.apply(make_text, axis=1)

# Creates severity labels based on thresholds
df['label'] = ((df['Hospitalizations'] >= 10) | 
               (df['Fatalities'] > 0) | 
               (df['Illnesses'] > 10)).astype(int)
2. PubMedBERT Fine-tuning
Base model: microsoft/BiomedNLP-PubMedBERT-base-uncased-abstract

Training epochs: 3

Learning rate: 2e-5

Batch size: 16

3. Outbreak Forecasting with BERT Embeddings
Extracts BERT embeddings from outbreak descriptions

Trains XGBoost regressor to predict monthly outbreak counts

Achieves RMSE of ~1.82 on test data

4. Running the Notebook
You can run the training notebook in Google Colab:

https://colab.research.google.com/assets/colab-badge.svg

Prerequisites for Colab:

Upload your outbreaks.csv file to Google Drive

Update the CSV_PATH in the notebook to point to your file

 Running Locally
Option 1: Run the Gradio App
Clone the repository:

bash
git clone https://github.com/hellosmallkat/food-poisoning-bert.git
cd food-poisoning-bert
Install dependencies:

bash
pip install -r requirements.txt
Run the app:

bash
python app.py
Open your browser to http://127.0.0.1:7860

Option 2: Run the Training Notebook
If you want to retrain the model:

bash
# Install Jupyter if you don't have it
pip install jupyter

# Launch the notebook
jupyter notebook Food_poisoning_outbreak_BERT_.ipynb

 Deployment
Hugging Face Space
The app is deployed on Hugging Face Spaces. To update:

Push changes to GitHub:

bash
git add .
git commit -m "Update app"
git push origin main
Hugging Face Space automatically syncs with the GitHub repository

GitHub Pages
The index.html file provides a custom landing page that embeds the Hugging Face Space:

Data Sources
CDC Foodborne Disease Outbreak Surveillance System (1998-2024)

FoodNet (Foodborne Diseases Active Surveillance Network)

NORS (National Outbreak Reporting System)

US Census Bureau (2023 state population estimates)

Pathogen Database
Disease	Type	Severity	Hospitalization Rate	Fatality Rate
Botulism	Bacteria	10/10	99%	5%
Listeria	Bacteria	9/10	94%	20%
Vibrio	Bacteria	8/10	50%	15%
Toxoplasma	Parasite	7/10	60%	3%
E. coli	Bacteria	6/10	30%	1%
Hepatitis A	Virus	5/10	22%	0.3%
Salmonella	Bacteria	4/10	20%	0.4%
 Future Improvements
Integrate fine-tuned PubMedBERT model into the Gradio app

Add time-series forecasting for outbreak prediction by state

Create interactive maps showing outbreak hotspots

Support for uploading custom datasets

Real-time CDC data API integration

Mobile-responsive design improvements

 Contributing
 
Contributions are welcome! Please feel free to submit a Pull Request.

License

This project is for educational and public health awareness purposes. Data is derived from public CDC sources.

Acknowledgments

CDC for providing comprehensive outbreak data

Hugging Face for hosting the Space

Microsoft for the PubMedBERT model

Built with: Python, PyTorch, Transformers, Gradio, XGBoost

Maintained by: kat4everokay

Live Demo: https://kat4everokay-food-poisoning-bert.hf.space
