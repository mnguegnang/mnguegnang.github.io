---
title: "Anomaly Detection in Power Generation Plants Using Machine Learning and Neural Networks"
collection: publications
category: manuscripts
permalink: /publication/2009-10-01-paper-title-number-1
excerpt: 'This paper applies machine learning and neural networks to analyze data from power generation plants for anomaly detection.'
date: 2020-11-19
venue: 'Applied Artificial Intelligence'
#slidesurl: 'https://academicpages.github.io/files/slides1.pdf'
paperurl: 'https://doi.org/10.1080/08839514.2019.1691839' #'https://academicpages.github.io/files/paper1.pdf'
#bibtexurl: 'https://academicpages.github.io/files/bibtex1.bib'
#citation: 'Your Name, You. (2009). &quot;Paper Title Number 1.&quot; <i>Journal 1</i>. 1(1).'
---

Reliable electricity supply is critical for industrial operations. In Cameroon, frequent load shedding has led the telecom industry to rely on backup power sources, particularly diesel generators. Managing fuel consumption for these generators is challenging due to inaccuracies in mechanical fuel level gauges and inadequate maintenance at base stations, which may result in fuel pilferage and excessive consumption.

To address these challenges, the study detects anomalies in recorded fuel consumption data by modeling consumption patterns using four classification techniques: support vector machines (SVM), K-Nearest Neighbors (KNN), Logistic Regression (LR), and MultiLayer Perceptron (MLP). The performance of these classifiers is evaluated and compared on test data. This research demonstrates the effectiveness of supervised machine learning classification techniques for anomaly detection in fuel consumption datasets from TeleInfra base stations powered by generators.

The dataset comprises recorded fuel consumption data from multiple TeleInfra base stations collected over one year. Although the data includes various power types, the analysis primarily focuses on base stations powered by generators. Feature engineering involved extracting variables such as generator working hours, consumption rate, total fuel consumed, and quantity of fuel added. Detected anomalies, or outliers, include instances where the generator operated for excessive hours in a day, consumption exceeded the generator's daily capacity, fuel consumed and operating time were zero. These outliers defined the classification classes, with selected samples labeled as anomalies and the remainder as normal.

Standard feature engineering and selection processes were implemented, followed by model fitting and performance evaluation on test data. Results indicate that the MLP classifier achieved the highest performance, with a 96% score in K-fold cross-validation. Due to class imbalance in the dataset, the precision-recall curve was used instead of the ROC curve, resulting in an Area Under Curve (AUC) value of 0.98.
