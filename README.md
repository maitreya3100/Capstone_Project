# Capstone_Project
# 🚦 Dynamic Parking Pricing Dashboard

A Python-based simulation and visualization tool for dynamic parking pricing.  
Implements two pricing models—**Model 1: Baseline Linear** and **Model 2: Demand‑Based**—and provides both static Matplotlib charts and an interactive Bokeh/Panel dashboard.

---

## 🔍 Pricing Models

### 1. Model 1: Baseline Linear Model  
A simple reference model where price updates linearly with occupancy rate.

\[
\text{Price}_{t+1} = \text{Price}_t \;+\; \alpha \;\times\;\bigl(\tfrac{\text{Occupancy}}{\text{Capacity}}\bigr)
\]

- **α** controls sensitivity to occupancy rate.  
- Ensures minimum price = base price.  

---

### 2. Model 2: Demand‑Based Price Function  
A more advanced model that constructs a **mathematical demand function** and then adjusts price smoothly.

1. **Compute raw demand**  
   \[
   \text{Demand} = \alpha\cdot\frac{\text{Occupancy}}{\text{Capacity}}
                  \;+\;\beta\cdot\text{QueueLength}
                  \;-\;\gamma\cdot\text{TrafficLevel}
                  \;+\;\delta\cdot\mathbb{1}(\text{SpecialDay})
                  \;+\;\varepsilon\cdot\text{VehicleWeight}
   \]
2. **Normalize demand** into \([0,1]\) to bound fluctuations.  
3. **Price update**  
   \[
   \text{Price} = \text{BasePrice}\times\bigl[\,1 \;+\;\lambda\cdot\text{NormalizedDemand}\bigr]
   \]  
   with overall price clamped between **0.5× base** and **2× base**.

---

## 🛠️ Tech Stack

- **Python 3.8+**  
- **Pandas**, **NumPy** — data handling & random sampling  
- **Matplotlib** — static charts  
- **Bokeh** + **Panel** — interactive dashboard & hover tools  
- **Pathway (pw)** — schema placeholder  

---

## 📁 Repository Layout
