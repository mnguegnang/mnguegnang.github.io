---
title: "Intelligent Fuel Consumption & Warning System"
excerpt: "A production-grade ML web app (Flask) for telecom power generation. Inspired by automotive predictive maintenance, it reduces fuel theft and optimizes logistics.<br/><img src='/images/fuel-app-input-interface.png' style='width:100%; margin-top:10px; border:1px solid #eee;'><br/><a href='https://github.com/mnguegnang/ML-app-fuelprediction.git' class='btn btn--info'>View Code</a>"
collection: portfolio
---

{% comment %}
---
title: "ML fuel consumption web application"
excerpt: "Machine learning web application to predict fuel in power generation plants<br/><img src='/images/fuel-app-input-interface.png' width='621' height='580' style='object-fit: contain;'><br/><a href='https://github.com/mnguegnang/ML-app-fuelprediction.git' class='btn btn--info'>View Code</a>"
collection: portfolio
---
{% endcomment %} 

## Overview
**Role:** End-to-End ML Engineer  
**Stack:** Python, Flask, Scikit-Learn, Pygal, Pandas  
**Deployment:** Live on **Render.com** (Dockerized)  
**Impact:** Secured ~84,617 Liters of fuel; Reduced emergency deliveries by 75%.

This project addresses a critical infrastructure challenge in Cameroon: managing fuel logistics for telecommunication base stations powered by diesel generators. Due to electrical shortages, these stations rely on generators, making fuel pilferage (theft) and operational inefficiency major economic drains.

## The "First Mover" Challenge
In 2018, Machine Learning applications in the power generation sector—specifically for remote, small-scale diesel generators—were virtually non-existent. Most research at the time focused on the automotive industry (car engines).

**Innovation:** We applied a **cross-domain transfer of methodology**. We studied ML techniques used for predicting fuel consumption in vehicles and adapted those feature engineering principles to stationary power plants. This allowed us to build one of the region's first predictive maintenance systems for telecom infrastructure.

<img src='/images/fuel_interface_main.png' alt='Main Application Interface' style='width: 100%; border: 1px solid #ddd; border-radius: 5px; margin-bottom: 20px; box-shadow: 0 4px 8px rgba(0,0,0,0.1);'>
*Figure 1: The application GUI. Note the 'JSON API' integration and dynamic column mapping, allowing users to upload varied Excel formats without preprocessing.*

## Technical Solution

### 1. The Machine Learning Core
We approached the problem by establishing a "baseline of truth" for consumption.
*   **Algorithm:** Random Forest Regressor (Scikit-Learn).
*   **Performance:** Achieved a **Nash-Sutcliffe Efficiency (NSE) of 0.986**.
*   **Deviation Warning System:** Unlike dedicated anomaly detection algorithms (like Isolation Forests), this system uses a threshold-based approach. It compares the *Predicted Consumption* (what the generator *should* have used based on load/hours) against the *Actual Reported Consumption*. If the deviation exceeds a statistical threshold, the site is flagged for manual inspection.

### 2. Engineering & Architecture (Flask)
This is not a monolithic script; it is a modular web application deployed on **Render.com**.

<div style="display: flex; flex-wrap: wrap; align-items: center; gap: 20px; margin-bottom: 20px;">
    <div style="flex: 1; min-width: 250px;">
        <img src='/images/fuel_file_structure.png' alt='Flask Project Structure' style='width: 100%; border: 1px solid #ddd; border-radius: 5px;'>
    </div>
    <div style="flex: 2; min-width: 300px;">
        <p><strong>Modular Design Pattern:</strong><br>
        As shown in the file structure, the codebase utilizes <strong>Flask Blueprints</strong> (<code>routes/</code>) to decouple the API logic from the visualization logic.</p>
        <ul>
            <li><code>utils/</code>: Contains isolated modules for validation, feature engineering, and chart generation.</li>
            <li><code>pkl_objects/</code>: Demonstrates efficient model serialization (Joblib) for fast inference on startup.</li>
            <li><code>logs/</code>: Implements production logging for debugging the remote Render deployment.</li>
        </ul>
    </div>
</div>

*   **Dynamic Feature Mapping:** The app handles inconsistent Excel headers dynamically, preventing pipeline failures.
*   **Interactive Visualization:** Integrated **Pygal** to generate lightweight SVG charts that users can interact with directly in the browser.

## Data Visualization & Insights

The application transforms raw Excel rows into decision-support dashboards.

<div style="display: flex; flex-wrap: wrap; gap: 20px; justify-content: center;">
    <div style="flex: 1; min-width: 300px;">
        <img src='/images/fuel_cluster_chart.png' alt='Cluster Analysis' style='width: 100%; border: 1px solid #eee; border-radius: 5px;'>
        <p style="text-align: center; font-style: italic; font-size: 0.9em;">Figure 3: Cluster Analysis identifying high-consumption regions.</p>
    </div>
    <div style="flex: 1; min-width: 300px;">
        <img src='/images/fuel_time_series.png' alt='Time Series Analysis' style='width: 100%; border: 1px solid #eee; border-radius: 5px;'>
        <p style="text-align: center; font-style: italic; font-size: 0.9em;">Figure 4: Time-series tracking showing consumption trends vs. moving averages.</p>
    </div>
</div>

## Business Value
*   **Cost Assurance:** The "Deviation Warning" system successfully identified discrepancies contributing to the security of **~84,617 liters** of fuel.
*   **Logistics:** Enabled proactive fuel planning, reducing expensive emergency fuel deliveries by an estimated **75%**.
*   **Efficiency:** Automated the ingestion of logs, reducing reporting time from days to seconds.

[Link to GitHub Repository] | [Link to Live App on Render]
