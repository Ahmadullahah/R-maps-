# ⭐ Community-Based Needs Assessment (CBNA) Data Analysis

![R](https://img.shields.io/badge/language-R-blue) ![Data Analysis](https://img.shields.io/badge/type-data%20analysis-green) ![Completed](https://img.shields.io/badge/status-completed-lightgrey)

---

## 📌 Project Overview

This repository demonstrates a **complete data analysis workflow in R**, focusing on **cleaning, harmonizing, and analyzing large multi-round survey datasets** from the **Community-Based Needs Assessment (CBNA)** program.

**Skills demonstrated:**

- Data cleaning and preprocessing  
- Data harmonization across multiple rounds  
- Exploratory and statistical analysis  
- Data visualization  

> The dataset contains information on *community vulnerabilities, access to services, infrastructure, and humanitarian needs*.

---

## 📊 Data Source

The analysis uses publicly available CBNA data provided by the  
[International Organization for Migration (IOM)](https://www.iom.int/) through the [Displacement Tracking Matrix (DTM)](https://dtm.iom.int/).  

**CBNA Dataset Features:**

- 6 rounds of data collection  
- ~12,000–16,000 communities per round  
- Multi-dimensional indicators covering **community needs, services, and living conditions**

🔗 [Access the CBNA Dataset](https://dtm.iom.int/data-product-series/community-based-needs-assessment-cbna)

---

## 🧹 Data Cleaning & Preparation

The raw CBNA datasets required **extensive cleaning**. Key steps included:

1. Importing and combining all six rounds  
2. Standardizing variable names and formats  
3. Removing duplicates and incomplete records  
4. Handling missing values  
5. Restructuring data for analysis  
6. Producing a **clean, harmonized dataset** ready for visualization and modeling  

> All steps are implemented in **R scripts**, ensuring **reproducibility**.

---

## ⚙️ Tools & Technologies

**Programming Language:** `R`  

**Packages Used:**

```r
install.packages(c("tidyverse", "dplyr", "ggplot2", "tidyr", "readr", "janitor"))
