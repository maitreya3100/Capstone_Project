# Dynamic Pricing for Urban Parking Lots

This repository contains the implementation of a real-time pricing engine for urban parking spaces. It was developed for the Summer Analytics 2025 Capstone, hosted by the Consulting & Analytics Club and Pathway.

Static pricing of parking lots can lead to overcrowding or underutilization. This project demonstrates how prices can be adjusted dynamically based on real‑time demand signals such as occupancy, queue length, traffic level, vehicle type, and special events.

---

## Table of Contents

1. [Project Overview]
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


# Architecture Overview

This document describes the high‑level architecture of the Dynamic Pricing for Urban Parking Lots system. It covers the main components, data flow, and interactions between modules.

---

## 1. System Components

1. **Data Ingestion Layer**  
   - **Pathway CSV Reader** (batch) or **Pathway Streaming Source** (real‑time)  
   - Validates incoming records against a predefined schema (`TrafficSchema`)  
   - Ensures timestamp ordering and fault tolerance  

2. **Feature Engineering & Processing**  
   - Calculates derived features such as:  
     - **occupancy_rate** = Occupancy / Capacity  
     - **traffic_score**: numeric encoding of traffic level  
     - **queue_length**, **is_special_day**, **vehicle_type_weight**  
   - Implements smoothing logic for stability  

3. **Pricing Engine**  
   - **Model 1 (Baseline Linear)**  
     - Simple update:  
       ```
       price[t+1] = price[t] + α × occupancy_rate
       ```  
   - **Model 2 (Demand‑Based)**  
     - Computes a composite demand score:  
       ```
       Demand = α·occupancy_rate
              + β·queue_length
              − γ·traffic_score
              + δ·is_special_day
              + ε·vehicle_type_weight
       ```  
     - Normalizes demand to [–1, +1] or [0, 1]  
     - Updates price:  
       ```
       price = BasePrice × (1 + λ × normalized_demand)
       ```  
   - Ensures price bounds (e.g., [0.5×, 2×] of base price)  

4. **Visualization & Dashboard**  
   - **Bokeh** for interactive plots (histograms, line charts, bar charts)  
   - **Panel** for layout, widgets (sliders, buttons), and data tables  
   - Supports inline rendering in Jupyter/Colab and as a standalone web app via `panel serve`  

5. **Deployment & Serving**  
   - **Local Development**: run notebooks in Google Colab or local Jupyter  
   - **Production**: `panel serve dashboard.py --show` or containerize with Docker  
   - (Optional) Expose via ngrok or cloud service for remote access  

---

## 2. Data Flow

```text
┌──────────────────────────┐
│  Raw CSV / Stream Input │
│  (Pathway Reader)       │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  Validation & Parsing    │
│  (Pathway Schema)        │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  Feature Engineering     │
│  (pandas & NumPy)        │
│  • occupancy_rate        │
│  • traffic_score         │
│  • queue_length          │
│  • special_day flag      │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  Pricing Engine          │
│  • Model 1 (Linear)      │
│  • Model 2 (Demand‑Based)│
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│  Dashboard & Visualization│
│  • Bokeh plots            │
│  • Panel layout           │
│  • Interactive controls   │
└───────────────────────────┘
```
## Workflow

The end‑to‑end workflow for the Dynamic Parking Pricing system consists of the following stages:

1. **Data Ingestion**  
   - **Batch mode**: Read historical/snapshot CSV files with `pw.io.csv.read(..., mode="static")`.  
   - **Streaming mode** (future extension): Ingest live updates using `mode="streaming"`.  
   - Schema validation ensures each record contains the required fields and correct types.

2. **Feature Engineering**  
   - Convert raw timestamps (`LastUpdatedDate` + `LastUpdatedTime`) into a unified `timestamp` column.  
   - Compute derived features in pandas/NumPy:  
     - `occupancy_rate = Occupancy / Capacity`  
     - `traffic_score` (encode “low”/“medium”/“high”)  
     - `queue_length`, `is_special_day`, and `vehicle_type_weight`  

3. **Pricing Model Execution**  
   - **Model 1** (Baseline Linear):  
     ```
     price[t+1] = price[t] + α × occupancy_rate
     ```  
   - **Model 2** (Demand‑Based):  
     1. Calculate composite demand:  
        ```
        D = α·occupancy_rate + β·queue_length – γ·traffic_score + δ·is_special_day + ε·vehicle_type_weight
        ```  
     2. Normalize `D` to [0,1] (or [–1,+1]).  
     3. Compute new price:  
        ```
        price = BasePrice × (1 + λ × normalized_D)
        ```  
   - Clip prices to predefined bounds (e.g. [0.5×, 2×] BasePrice) for stability.

4. **Simulation Loop**  
   - Initialize `current_price` = BasePrice.  
   - For each time step (or batch window):  
     - Update features on the latest data.  
     - Recompute price according to the selected model.  
     - Append to `price_history` for trend analysis.

5. **Visualization & Dashboard**  
   - Instantiate Bokeh figures: histograms, line charts, bar charts.  
   - Bind data via `ColumnDataSource`.  
   - Use Panel to compose layouts (`pn.Row`, `pn.Column`), widgets (sliders, buttons), and data tables.  
   - In Colab: call `output_notebook()` + `show(fig)` for inline plots.  
   - In production: launch with `panel serve dashboard.py --show`.

6. **Extension Points**  
   - **Model 3** (Competitive Pricing): add a spatial layer to compare nearby lot prices and suggest rerouting.  
   - **Real‑time Streaming**: switch to `mode="streaming"` and hook into a message queue.  
   - **Persistence**: store historical prices in a database for long‑term analysis and reporting.  

---

### Workflow Diagram

```text
┌─────────────────┐
│ Data Ingestion  │<─── CSV or Streaming ───┐
│  (Pathway)      │                          │
└───────┬─────────┘                          │
        │                                    │
        ▼                                    │
┌─────────────────┐                          │
│ Feature         │                          │
│ Engineering     │                          │
│ (pandas/NumPy)  │                          │
└───────┬─────────┘                          │
        │                                    │
        ▼                                    │
┌─────────────────┐      ┌───────────────┐   │
│ Pricing Model   │◀─────┤ Parameter     │   │
│ Execution       │      │ Controls      │   │
│ (Model 1 & 2)   │      └───────────────┘   │
└───────┬─────────┘                          │
        │                                    │
        ▼                                    │
┌─────────────────┐                          │
│ Visualization   │                          │
│ & Dashboard     │──► Colab or Web Server   │
│ (Bokeh + Panel) │                          │
└─────────────────┘                          │
                                             │
           Extension Points ─────────────────┘








