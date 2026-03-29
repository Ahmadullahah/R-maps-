⭐ Community-Based Needs Assessment (CBNA) Data Analysis

📌 Project Overview

This repository demonstrates a complete data analysis workflow in R, focusing on cleaning, harmonizing, and analyzing large multi-round survey datasets from the Community-Based Needs Assessment (CBNA) program.

Skills demonstrated:

Data cleaning and preprocessing
Data harmonization across multiple rounds
Exploratory and statistical analysis
Data visualization

The dataset contains information on community vulnerabilities, access to services, infrastructure, and humanitarian needs.

📊 Data Source

The analysis uses public CBNA data provided by the
International Organization for Migration (IOM)
 through the Displacement Tracking Matrix (DTM)
.

CBNA Dataset Features:

6 rounds of data collection
~12,000–16,000 communities per round
Multi-dimensional indicators covering community needs, services, and living conditions

🔗 Access the CBNA Dataset

🧹 Data Cleaning & Preparation

The raw CBNA datasets required extensive cleaning to be ready for analysis. Steps included:

Importing and combining all six rounds
Standardizing variable names and formats
Removing duplicates and incomplete records
Handling missing values
Restructuring data for analysis
Producing a clean, harmonized dataset for visualization and modeling

All steps are implemented in R scripts for full reproducibility.

⚙️ Tools & Technologies

Programming Language: R

Packages Used:

install.packages(c("tidyverse", "dplyr", "ggplot2", "tidyr", "readr", "janitor"))
tidyverse – data manipulation & visualization
dplyr – data transformation
ggplot2 – plotting and charts
tidyr – data reshaping
readr – importing CSV/text files
janitor – cleaning column names & data
📂 Repository Structure
.
├── data/
│   ├── raw/                # Original CBNA datasets
│   └── cleaned/            # Processed datasets ready for analysis
├── scripts/
│   ├── data_cleaning.R     # Cleaning & harmonization
│   ├── analysis.R          # Statistical analysis
│   └── visualization.R     # Figures and visualizations
├── figures/                # Generated charts
├── results/                # Analysis outputs
└── README.md
🔎 Methodology

The workflow follows five main stages:

1️⃣ Data Acquisition – Downloaded all six rounds of CBNA data
2️⃣ Data Cleaning & Harmonization – Standardized and merged datasets
3️⃣ Exploratory Data Analysis (EDA) – Summary statistics and trends
4️⃣ Statistical Analysis – Identify patterns and relationships
5️⃣ Visualization & Reporting – Create charts and figures to illustrate findings

📈 Key Results
Cleaned and combined datasets covering ~12k–16k communities per round
Descriptive statistics of community needs
Visualizations of service access, infrastructure, and vulnerabilities
Reproducible workflow demonstrating full data analysis pipeline

All results, figures, and outputs are included in the results/ and figures/ directories.

🔁 Reproducibility

Run scripts in the following order to reproduce the workflow:

Rscript scripts/data_cleaning.R
Rscript scripts/analysis.R
Rscript scripts/visualization.R

This will generate:

Cleaned dataset
Analytical results
Figures and charts
👤 Author & Contact

Your Name
Portfolio Data Analysis Project

Email: your.email@example.com

LinkedIn: Your Profile

📚 Acknowledgements

Data provided by the International Organization for Migration via the Displacement Tracking Matrix program.

⚠️ Disclaimer

The original CBNA data is produced by IOM.
All cleaning, analysis, and visualizations were performed by the author for portfolio purposes.
