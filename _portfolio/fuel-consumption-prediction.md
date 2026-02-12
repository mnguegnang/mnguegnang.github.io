---
title: "Intelligent Fuel Consumption & Warning System"
excerpt: "A production-grade ML web app (Flask) for telecom power generation. Inspired by automotive predictive maintenance, it reduces fuel theft and optimizes logistics.<br/><img src='/images/fuel-app-input-interface.png' style='width: 100%; max-width: 650px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'><br/><a href='https://github.com/mnguegnang/ML-app-fuelprediction.git' class='btn btn--info'>View Code</a>"
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
**Deployment:** Live on **Render.com**   
**Impact:** Secured approximately 84617 liters of fuel and improved fuel consumption and logistics.

This project addresses a key infrastructure challenge in Cameroon: managing excessive fuel consumption and logistics for telecom base stations powered by diesel generators. Frequent electrical shortages force reliance on generators, leading to high fuel use, pilferage, and operational inefficiencies that create significant economic burdens.

## **Industry Context & Challenge**
In 2018, Machine Learning applications for remote areas that rely on the power generation plants were virtually non-existent. Most research focused on automotive engines.

**Cross-domain methodology:** We adapted ML techniques from vehicle fuel consumption prediction to stationary power generation plants. This approach enabled us to develop one of the region’s first predictive maintenance systems for telecom base stations.
<img src='/images/fuel-app-input-interface.png' alt='Main Application Interface' style='width: 100%; max-width: 700px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'>
*Figure 1: The application GUI. Note the 'JSON API' integration and dynamic column mapping, allowing users to upload varied Excel formats without preprocessing.*

## Technical Solution

### 1. The Machine Learning Core
We addressed the problem by establishing a baseline for accurate fuel consumption.
*   **Algorithm:** Random Forest Regressor (Scikit-Learn).
*   **Performance:** Achieved a **Nash-Sutcliffe Efficiency (NSE) of 0.986**.
*   **Deviation Warning System:** The system uses a threshold based on the mean plus two standard deviations. If consumption exceeds this threshold, the site is flagged for investigation. The team then verifies the alert and initiates necessary maintenance or logistics actions. This process ensures prompt response to deviations, reducing risks and improving operational efficiency.
### 2. Engineering & Architecture (Flask)
The project is a fully deployed web application on **Render.com**

<!-- START ARCHITECTURE SECTION -->
<div style="background-color: #f8f9fa; border: 1px solid #e1e4e8; border-radius: 6px; padding: 20px; margin-bottom: 20px;">
    
    <h3 style="margin-top: 0; border-bottom: 1px solid #eaecef; padding-bottom: 10px;">System Architecture</h3>
    
    <div style="display: flex; flex-wrap: wrap; gap: 20px; align-items: flex-start;">
        
        <!-- LEFT COLUMN: FILE TREE (Fixed with <pre> tag) -->
        <div style="flex: 1; min-width: 300px;">
<pre style="background: #2d333b; color: #adbac7; padding: 15px; border-radius: 6px; font-family: 'Consolas', 'Monaco', monospace; font-size: 0.85em; line-height: 1.4; margin: 0; overflow-x: auto; white-space: pre; border: none;">
fuel_prediction_app/
├── columns_app.py
├── config.py
├── pkl_objects/
│   └── filename.joblib
├── uploads/
├── logs/
├── templates/
├── utils/
│   ├── file_utils.py
│   ├── validation_utils.py
│   ├── data_utils.py
│   ├── model_utils.py
│   ├── metrics_utils.py
│   ├── chart_utils.py
│   └── export_utils.py
└── routes/
    ├── main_routes.py
    ├── visualization_routes.py
    └── export_routes.py
</pre>
        </div>

        <!-- RIGHT COLUMN: EXPLANATION -->
        <div style="flex: 1; min-width: 280px;">
            <p style="margin-top: 0;"><strong>Modular Design Pattern</strong><br>
            The project uses <strong>Flask Blueprints</strong> to decouple API logic from visualization.</p>
            
            <ul style="padding-left: 20px;">
                <li style="margin-bottom: 10px;">
                    <code>utils/</code><br>
                    <span style="font-size: 0.9em; color: #666;">Contains pure Python logic (validation, feature engineering) isolated from the web framework. This allows for unit testing without the Flask context.</span>
                </li>
                <li style="margin-bottom: 10px;">
                    <code>routes/</code><br>
                    <span style="font-size: 0.9em; color: #666;">Handles HTTP requests. By separating <em>Visualization</em> routes from <em>Export</em> routes, the codebase remains readable.</span>
                </li>
                <li style="margin-bottom: 10px;">
                    <code>pkl_objects/</code><br>
                    <span style="font-size: 0.9em; color: #666;">The Random Forest model is serialized using Joblib and loaded into memory <strong>once</strong> at startup, reducing inference latency.</span>
                </li>
            </ul>
        </div>
        
    </div>
</div>
<!-- END ARCHITECTURE SECTION -->

*   **Dynamic Feature Mapping:** As seen in Figure 1, the UX allows users to map their dataset columns to the model’s inputs dynamically, accommodating inconsistent naming conventions in source files.
*   **Interactive Visualization:** Integrated **Pygal** to generate lightweight SVG charts that users can interact with directly in the browser.

## Data Visualization & Insights

The application offers a decision-support dashboard that translates model outputs through five interactive views, accessible from the top navigation bar. These views enable facility managers to analyze data by:
*   **Cluster View:** Visualizes fuel consumption aggregated by region.
*   **Site View (Top 20):** Instantly identifies the highest-consumption sites to help prioritize maintenance.
*   **Distribution Analysis:** Uses histograms to display the spread of fuel usage.
*   **Time Series:** Tracks consumption trends over time to visualize seasonal patterns.

<img src='/images/fuel_cluster_chart.png' alt='Cluster Analysis' style='width: 100%; max-width: 700px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'>
*Figure 2: Fuel consumption across clusters.*

<img src='/images/fuel_time_series.png' alt='Time Series Analysis' style='width: 100%; max-width: 700px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'>
*Figure 3: Time-series tracking showing consumption trends vs. moving averages.*

The application visualizes predicted fuel consumption per cluster, as shown in Figure 3. This enables rapid identification of consumption patterns and highlights high-consumption clusters.
**On-Demand Export & Reporting**  
To support external business workflows such as audits or executive presentations, the app includes a dedicated export engine. Users can download:
*   **Raw Data:** CSV/Excel files for archival and further analysis.
*   **Visual Assets:** High-resolution **SVG (Vector)** and **PNG** charts ready for insertion into PowerPoint or technical reports.
## Business Value
*   **Cost Assurance:** The project identified discrepancies that helped secure approximately **84617 liters** of fuel.
    The time-series feature enables analysis of fuel consumption patterns, helping identify trends, seasonal variations, and unusual fuel consumption over time.
*   **Logistics:** Enabled proactive fuel planning, reduced costly emergency deliveries.
*   **Efficiency:** Automated log ingestion, reducing reporting time from days to seconds.

## MLOps: Production Monitoring & Quality Assurance
Unlike static models, this application features a System Monitoring Dashboard to ensure production reliability
This dedicated interface provides real-time health checks on the ML pipeline:
1.  **Model Performance:** Tracks the **NSE (0.981)** score on the current batch to ensure prediction accuracy remains high.
2.  **System Health:** Monitors API success rates and processing latency.
3.  **Cache Statistics:** Displays the status of the server-side caching engine, which is critical for preventing timeouts on the Render.com free tier when processing large datasets.

<img src='/images/dashboard_system_monitoring.png' alt='System Monitoring Dashboard' style='width: 100%; max-width: 700px; height: auto; display: block; margin: 20px auto; border: 1px solid #ddd; border-radius: 5px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);'>
*Figure 4: The Monitoring Dashboard. This internal view allows engineering teams to audit model metrics and verify that the data cache is active.*


<a href='https://github.com/mnguegnang/ML-app-fuelprediction.git' class='btn btn--info'>View Code</a>  <a href='https://ml-app-fuelprediction.onrender.com' class='btn btn--info'>Live App on Render</a>
