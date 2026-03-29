⭐ Community-Based Needs Assessment (CBNA) Analysis
Data Cleaning, Processing, and Analysis in R






📌 Project Overview

This repository contains the code, cleaned datasets, analysis results, and visualizations developed for my academic thesis project.

The project analyzes community-level information from the Community-Based Needs Assessment (CBNA) dataset collected by the International Organization for Migration through the Displacement Tracking Matrix program.

The goal of this project is to explore patterns in community vulnerabilities, access to services, and infrastructure conditions using statistical analysis and visualization tools in R.

📊 Data Source

The data used in this project originates from the Community-Based Needs Assessment (CBNA) data series.

🔗 Official dataset source:
https://dtm.iom.int/data-product-series/community-based-needs-assessment-cbna

The CBNA dataset collects detailed information about community conditions across thousands of settlements, including indicators related to:

access to basic services
infrastructure conditions
displacement and migration contexts
humanitarian needs
community vulnerabilities

The original dataset is produced and maintained by the International Organization for Migration.

🧹 Data Preparation and Cleaning

The raw CBNA dataset is distributed across six different rounds of data collection, each covering approximately 12,000 to 16,000 communities or settlements.

Because the original data is spread across multiple rounds and contains inconsistencies in structure and formatting, a significant portion of this project focused on data preparation and cleaning.

The cleaning process included:

importing datasets from all six rounds
harmonizing variable names and formats across rounds
removing duplicates and incomplete records
handling missing values
restructuring variables for analysis
combining the rounds into a consistent analytical dataset

All cleaning steps were implemented using R scripts, allowing the entire process to be transparent and reproducible.

⚙️ Tools and Technologies

The analysis was conducted using R and several widely used data analysis packages.

Main packages used include:

tidyverse
dplyr
ggplot2
tidyr
readr
janitor

These tools were used for data cleaning, exploratory analysis, and visualization.

📂 Repository Structure
.
├── data
│   ├── raw/                 # Original CBNA datasets
│   └── cleaned/             # Processed datasets used for analysis
│
├── scripts
│   ├── data_cleaning.R      # Cleaning and harmonizing datasets
│   ├── analysis.R           # Statistical analysis
│   └── visualization.R      # Creation of plots and figures
│
├── figures                  # Generated visualizations
├── results                  # Tables and analysis outputs
└── README.md
🔎 Methodology

The analysis workflow follows several stages:

1️⃣ Data acquisition from the CBNA portal
2️⃣ Data cleaning and harmonization across multiple rounds
3️⃣ Exploratory data analysis (EDA)
4️⃣ Statistical analysis of community indicators
5️⃣ Visualization of key findings

This workflow ensures that the entire analysis can be reproduced by running the provided R scripts.

📈 Results

The repository includes:

cleaned datasets ready for analysis
statistical outputs
figures and visualizations generated during the analysis
R scripts documenting each stage of the workflow

These outputs illustrate patterns in community needs, access to services, and infrastructure conditions across the assessed settlements.

🔁 Reproducibility

All data processing and analysis steps are documented through R scripts included in the repository.

Running the scripts in sequence will reproduce:

the cleaned dataset
analytical results
figures used in the project
👤 Author

Your Name
Academic Thesis Project
University Name
Year

📚 Acknowledgements

Data provided by the International Organization for Migration through the Displacement Tracking Matrix initiative.

⚠️ Disclaimer

The original data is produced by the International Organization for Migration.
The data cleaning, processing, analysis, and interpretation presented in this repository are the responsibility of the author.
