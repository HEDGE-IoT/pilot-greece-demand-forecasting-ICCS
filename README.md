# Demand Forecasting

## 1.1 General Information and Purpose
- **Service Name/Title:** `demand_forecasting`
- **Description and purpose:** Accurately forecast customer demand to assess energy flexibility capabilities and support market participation decisions.
- **Owner/Contact Information:** ICCS

---

## 1.2 Functional Requirements
1. The system should collect and process real-time data from IoT devices and smart meters.
2. The system should access historical consumption data provided by energy suppliers.
3. The system should perform data pre-processing, including cleaning and normalization.
4. The system should generate accurate short-term and long-term demand forecasts.
5. The system should support the integration of demand forecasts into optimization algorithms for market bidding.
6. The system should provide system operators with forecast data to identify grid issues.

---

## 1.3 Non-Functional Requirements
- Forecasts should be available in real-time or near real-time (within a few seconds).
- The accuracy of demand forecasts should be maintained at a high level to support decision-making.
- System uptime should meet a minimum of 99.9%.
- Access to forecasts should be restricted to authorized users.
- The system should maintain periodic data backups with automated recovery.

---

## 1.4 Service Interfaces

### 1.4.1 API Endpoints

#### Endpoint 1 — Train Demand Forecasting Model
- **URL:** `/forecast/demand/train`
- **Method:** `POST`
- **Description:** Triggers training or retraining of the consumption forecasting model using historical data and available features (e.g., weather, IoT sensor data).

**Headers**
- `Content-Type: application/json`
- `Authorization: Bearer <token>`

**Request Example**
```json
{
  "start_time": "2023-01-01T00:00:00Z",
  "end_time": "2024-01-01T00:00:00Z",
  "granularity": "hourly",
  "customer_id": "CUST456"
}
```

**Response Example**
```json
{
  "message": "Model training initiated successfully",
  "model_id": "M456",
  "status": "queued",
  "timestamp": "2025-06-16T10:15:00Z"
}
```

**Error Handling**
- `400 Bad Request`: Invalid input parameters  
- `401 Unauthorized`: Invalid token  
- `500 Internal Server Error`: Model training failed  

---

#### Endpoint 2 — Get Demand Forecast
- **URL:** `/forecast/demand`
- **Method:** `GET`
- **Description:** Provides a forecast of energy demand for a specified time range.

**Headers**
- `Content-Type: application/json`
- `Authorization: Bearer <token>`

**Request Example**
```json
{
  "start_time": "2024-10-01T00:00:00Z",
  "end_time": "2024-10-01T12:00:00Z",
  "granularity": "hourly",
  "model_id": "M456",
  "customer_id": "CUST123"
}
```

**Response Example**
```json
{
  "status": "200 OK",
  "model_id": "M456",
  "data": [
    { "timestamp": "2024-10-01T00:00:00Z", "demand": 300.5 },
    { "timestamp": "2024-10-01T01:00:00Z", "demand": 290.7 }
  ]
}
```

**Error Handling**
- `400 Bad Request`: Invalid data format  
- `401 Unauthorized`: Invalid token  

---

## 1.5 UI Mock-ups
The user interface (embedded in the Aggregator Platform) should allow users to:  
- Input the start and end time for forecasts.  
- Select granularity options.  
- View forecasts as interactive charts or tables.  
- Download forecasts in CSV, PDF, or JPEG formats.  

UI elements include:  
- **Date-Time Picker** for selecting ranges.  
- **Granularity Dropdown** for forecast resolution.  
- **Display Panel** with charts/tables.  
- **Error Messages** for invalid inputs or errors.  

---

## 1.6 Data Model

### Entities and Relationships
- **Users** can manage one or more **Houses**.  
- **Houses** contain **Devices** (regular appliances or energy meters).  
- Users can belong to **Energy Communities** managed by **Aggregators**.  
- **PV Parks** and private **PV Systems** are linked to houses or communities.  

### Database Schema
- **Real-Time Energy Measurements:** High-frequency IoT data (current, voltage, power, energy).  
- **Aggregated Energy Usage:** Summarized hourly/daily/quarterly consumption.  
- **Metadata Schema:** Models users, houses, devices, PV systems, and energy communities.  

---

## 1.7 Integration and Dependencies
- **LFM API Interface (via Bid System):** Forecasts support bid formation, pricing, and LFM operations.  
- **Internal Data Sources:** Historical consumption data, IoT telemetry, and past bids.  
- **Edge and Cloud Integration:** Edge devices and cloud infrastructure for real-time inputs and forecasts.  
- **Planned App Store/Data Space Integration:** Shared models and compliance.  

### Dependencies
- **External:** IoT connectivity providers.  
- **System:** ML/AI forecasting modules, system database, UIs.  
- **Third-Party:** None currently.  

---

## 1.8 Security and Privacy
- **Data Sensitivity:** Customer-level usage forecasts, DER profiles, and market-sensitive outputs.  
- **Encryption:** TLS 1.2+ in transit, AES-256 at rest, encrypted backups.  
- **RBAC:** Aggregator Operator (forecasts, training), System Admin (full access), Data Analyst (anonymized only).  
- **Audit Logging:** Logs of forecast/training requests with timestamps, model IDs, and roles.  
- **Anomaly Detection:** Flags unusual forecast requests, data injection attempts, and unauthorized access.  

---
An end-to-end forecasting pipeline was implemented and tested with IoT demo data.  

### Functionality
- **Data Ingestion:** Aggregated 15-min/hourly usage data from PostgreSQL.  
- **Feature Engineering:** Rolling stats, lagged values, time-based features.  
- **Normalization:** MinMaxScaler with serialized scalers stored in MinIO.  
- **Models:** Multi-step Bi-GRU, recursive Bi-GRU/LSTM for aggregated and individual forecasts.  
- **Model Persistence:** Models, scalers, metrics stored in MinIO with versioning.  
- **Resilience:** Exception-safe, audit-compliant, handles missing data, supports low-latency inference.  

### Results
- Aggregated short-term (15 min): MAE = 0.17 kWh, MSE = 0.06, R² = 0.77.  
- Individual short-term (15 min): MAE = 0.028 kWh, MSE = 0.005, R² = 0.64.  

The system demonstrated strong accuracy at both aggregated and household levels.  
