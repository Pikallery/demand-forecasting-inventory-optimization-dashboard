# 📊 Demand Forecasting & Inventory Optimization Dashboard

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://pikallery-demand-forecasting-inventory-optimization--app-j7el4t.streamlit.app/)
[![Python 3.9+](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104%2B-009688.svg)](https://fastapi.tiangolo.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An end-to-end interactive decision-support system and machine learning prototype for retail demand forecasting and inventory replenishment optimization.

---

## 🌐 Live Application & Project Links

- **🚀 Live Interactive Dashboard**: **[Demand Forecasting & Inventory Optimization Dashboard · Streamlit](https://pikallery-demand-forecasting-inventory-optimization--app-j7el4t.streamlit.app/)**
- **💻 Standalone Dashboard Repository**: [Pikallery/demand-forecasting-inventory-optimization-dashboard](https://github.com/Pikallery/demand-forecasting-inventory-optimization-dashboard)
- **👥 Team Collaborative Repository**: [prajwalkumargrti-collab/demand-forecasting-inventory-optimization](https://github.com/prajwalkumargrti-collab/demand-forecasting-inventory-optimization)
- **🔀 Team Pull Request (Role 5)**: [Pull Request #1 on Team Repo](https://github.com/prajwalkumargrti-collab/demand-forecasting-inventory-optimization/pull/1)
- **📦 Team Inventory Module Reference**: [Kotipallipujitha-29/Optimization.py](https://github.com/Kotipallipujitha-29/Optimization.py)

---

## 📁 Repository Structure

```text
demand-forecasting-dashboard/
├── app.py                          # Streamlit multi-page dashboard
├── api.py                          # FastAPI decision-support REST microservice
├── Dockerfile                      # Container definition for Docker/Render/HuggingFace
├── render.yaml                     # Render.com deployment configuration
├── requirements.txt                # Python dependencies
├── README.md                       # Comprehensive project documentation
├── .gitignore                      # Git ignore file
├── .streamlit/
│   └── config.toml                 # Streamlit theme & server configuration
├── data/
│   └── raw/
│       └── sales_data.csv          # 76,000 retail records (2022-01 to 2024-01)
├── src/
│   ├── __init__.py
│   ├── config.py                   # Central configuration & dynamic data path resolution
│   ├── data_loader.py              # Ingestion, cleaning, calendar feature engineering, dynamic multi-filters
│   ├── forecasting.py              # ML feature extraction, Random Forest training, naive baseline, rolling forecast
│   └── inventory.py                # Safety stock, dynamic reorder point, target stock, and risk scoring
└── views/
    ├── __init__.py
    ├── demand_overview.py          # Page 1: Demand KPIs, trendline, category/region/seasonal breakdowns, heatmap
    ├── product_store_forecasts.py  # Page 2: Store/product trajectories, actual vs forecast with 95% CI, 30-day outlook
    ├── inventory_risk.py           # Page 3: Stockout risk heatmap, replenishment orders, ROP status
    └── model_performance.py        # Page 4: Random Forest vs Naive baseline, residual error, feature importance
```

---

## 🗂️ Dataset Summary

- **Total Records**: 76,000 daily observations
- **Date Range**: January 1, 2022 – January 30, 2024
- **Dimensions**: 5 Stores (`S001`–`S005`) × 20 Products (`P0001`–`P0020`)
- **Product Categories**: Clothing, Electronics, Furniture, Groceries, Toys
- **Geographic Regions**: North, South, East, West
- **External Signals**: Price, Discount, Promotion, Competitor Pricing, Seasonality, Weather Condition, Epidemic
- **Target Variable**: `Demand` (Range: 4 to 430 units; Mean: ~104)

---

## 🖥️ Dashboard Views

| Page | Description |
| :--- | :--- |
| **📈 Demand Overview** | Aggregate metrics, daily time series line chart, category demand horizontal bars, regional donut chart, seasonal bars, and Store × Product average demand heatmap. |
| **🔮 Product/Store Forecasts** | Interactive filters for any Store/Product pair. Displays full historical trajectory, **Actual vs Forecast overlay** with 95% confidence intervals, and a **30-day recursive forward forecast** table. |
| **📦 Inventory Risk** | Stock status classification (`REORDER NOW`, `LOW STOCK`, `SUFFICIENT`), Store × Product **Stockout Risk Heatmap** (0–100 scale), detailed inventory table with conditional formatting, and stacked replenishment order bar charts. |
| **🎯 Model Performance** | Validation metrics (MAE ~22.86, RMSE ~30.82), **Naive Baseline Comparison** (MAE ~48.61, achieving **>53% error reduction**), residual error histogram, forecast bias meter, top-15 feature importances, and error distribution by product category. |

---

## 📦 Inventory Optimization Formulation

| Parameter | Value | Definition |
| :--- | :--- | :--- |
| **Lead Time ($L$)** | 3 days | Time required from order placement to stock arrival |
| **Review Period ($R$)** | 7 days | Periodic inventory review interval |
| **Service Level ($Z$)** | 1.65 | Standard normal score for 95% cycle service level |
| **Demand Lookback** | 28 days | Historical window for daily demand mean ($\mu_d$) and variance ($\sigma_d$) |

- **Safety Stock ($SS$)**: $SS = Z \times \sigma_d \times \sqrt{L + R}$
- **Reorder Point ($ROP$)**: $ROP = (\mu_d \times L) + SS$
- **Target Stock ($S_{target}$)**: $S_{target} = (\mu_d \times (L + R)) + SS$
- **Recommended Order Quantity**: $Q = \max(0, S_{target} - \text{Current Inventory})$

---

## 💻 Local Installation & Setup

```bash
# 1. Clone repository
git clone https://github.com/Pikallery/demand-forecasting-inventory-optimization-dashboard.git
cd demand-forecasting-inventory-optimization-dashboard

# 2. Create virtual environment
python -m venv venv
venv\Scripts\activate      # Windows
source venv/bin/activate   # macOS / Linux

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch the dashboard
streamlit run app.py
```
> The dashboard will automatically open in your browser at `http://localhost:8501`.

---

## 🔌 Headless Decision-Support REST API (FastAPI)

```bash
# Start FastAPI server
uvicorn api:app --host 0.0.0.0 --port 8000 --reload
```
Interactive API documentation: **`http://localhost:8000/docs`**

### Available Endpoints:
- `GET /health` — Service health and dataset summary.
- `GET /forecast?store_id=S001&product_id=P0001&horizon=30` — Validated demand forecast for specified store and product.
- `GET /inventory?store_id=S001` — Replenishment parameters, stockout risk scores, and recommended order quantities.
- `GET /stores` — List all valid store identifiers.
- `GET /products` — List all valid product identifiers.

---

## 🐳 Docker Deployment

```bash
# Build Docker image
docker build -t demand-forecasting-dashboard .

# Run container
docker run -p 8501:8501 demand-forecasting-dashboard
```
Access at `http://localhost:8501`.

---

## 📄 License
MIT License.
