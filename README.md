# Dynamic Pricing for Urban Parking Lots

This repository contains the implementation of a real-time pricing engine for urban parking spaces. It was developed for the Summer Analytics 2025 Capstone, hosted by the Consulting & Analytics Club and Pathway.

Static pricing of parking lots can lead to overcrowding or underutilization. This project demonstrates how prices can be adjusted dynamically based on real‑time demand signals such as occupancy, queue length, traffic level, vehicle type, and special events.

---

## Table of Contents

1. [Project Overview](#project-overview)  
2. [Tech Stack](#tech-stack)  
3. [Architecture](#architecture)  
4. [Model Descriptions](#model-descriptions)  
   - [Model 1: Baseline Linear Pricing](#model-1-baseline-linear-pricing)  
   - [Model 2: Demand‑Based Pricing](#model-2-demand-based-pricing)  
5. [Usage Instructions](#usage-instructions)  
6. [Project Structure](#project-structure)  
7. [License and Contributors](#license-and-contributors)  

---

## Project Overview

Urban parking spaces are a limited resource. Fixed pricing does not respond to fluctuating demand, leading to inefficiency. This project simulates a system that:

- Ingests streaming or batch data for multiple parking lots  
- Computes demand features (occupancy rate, queue length, traffic level, special events, vehicle type)  
- Applies pricing models in real time  
- Visualizes results in interactive dashboards  

---

## Tech Stack

| Component        | Purpose                                 |
|------------------|-----------------------------------------|
| Python           | Core programming language               |
| Pandas           | Data ingestion and manipulation         |
| NumPy            | Numerical computations                  |
| Pathway          | Real-time data simulation and processing|
| Bokeh            | Interactive plotting in notebooks       |
| Panel            | Dashboard layout and controls           |
| Google Colab     | Execution environment for notebooks     |
| Matplotlib       | Static plotting (optional)              |

---

## Architecture

[ Data Generation (Pathway or CSV) ]
│
▼
[ Feature Engineering (occupancy_rate, queue_length, traffic_score, etc.) ]
│
▼
[ Pricing Models ]
├─ Model 1: Linear price update by occupancy rate
└─ Model 2: Demand function with multiple features
│
▼
[ Visualization ]
├─ Occupancy distribution histogram
├─ Price evolution line chart
└─ Bar charts for system-level metrics

yaml
Copy
Edit

---

## Model Descriptions

### Model 1: Baseline Linear Pricing

- **Logic**: Price at time t+1 = Price t + α × (Occupancy / Capacity)  
- **Characteristics**:  
  - Simple, smooth, interpretable  
  - Acts as a reference baseline  

**Notebook**: `model_1_colab.ipynb`  

---

### Model 2: Demand‑Based Pricing

- **Demand function**:  
Demand = α·(Occupancy/Capacity)
+ β·QueueLength
− γ·TrafficLevel
+ δ·IsSpecialDay
+ ε·VehicleTypeWeight

markdown
Copy
Edit
- **Pricing**:  
Price_t = BasePrice × (1 + λ × NormalizedDemand)

yaml
Copy
Edit
- **Characteristics**:  
- Incorporates multiple real‑time factors  
- Ensures price variations remain bounded and smooth  

**Notebook**: `model_2_colab.ipynb`  

---

## Usage Instructions

### 1. Google Colab

- Open `model_1_colab.ipynb` or `model_2_colab.ipynb` in Colab  
- Install dependencies in the first cell:  
```bash
!pip install panel bokeh pathway pandas numpy
Run all cells to load data, compute prices, and render plots

2. Local Setup
Clone the repository:

bash
Copy
Edit
git clone https://github.com/yourusername/parking-dynamic-pricing.git
cd parking-dynamic-pricing
Install dependencies:

bash
Copy
Edit
pip install -r requirements.txt
Launch a Panel server for the notebook:

bash
Copy
Edit
panel serve model_1_colab.ipynb --show
Project Structure
bash
Copy
Edit
.
├── model_1_colab.ipynb      # Baseline linear pricing model
├── model_2_colab.ipynb      # Demand‑based pricing model
├── dataset.csv              # Sample or streamed data file
├── requirements.txt         # Python dependencies
└── README.md                # Project documentation
License and Contributors
License: MIT License
Authors:














