# Vision

Vision is a retail analytics and forecasting platform that helps a business upload sales data, clean and store it in a database, analyze performance by branch, and generate short-term sales predictions with AI.

The project is split into two main parts:

- Frontend: a dashboard that lets users explore metrics, predictions, and raw data
- Backend: a FastAPI API that processes Excel files, stores data in PostgreSQL, enriches it with weather and holiday data, computes business KPIs, and runs the forecasting model

## Architecture overview

The system works as a full pipeline:

1. A user uploads an Excel file from the frontend.
2. The backend validates the workbook, cleans and normalizes the sales data, and stores it in PostgreSQL.
3. Weather and holiday information is pulled from external sources and linked to the sales records.
4. The ML pipeline prepares the data and generates store-specific forecasting artifacts.
5. The frontend queries the backend for metrics and predictions and renders charts, tables, and KPIs.

## Backend responsibilities

The backend lives in the [back/](/Users/fedeb/OneDrive/Documentos/Vision-1/back) folder and is built with FastAPI.

It is responsible for:

- Receiving uploaded Excel files and validating the supported formats (.xls and .xlsx)
- Cleaning business data and mapping it into a normalized database model
- Saving data into PostgreSQL tables such as:
  - sucursal
  - ventas
  - productos
  - detalle_ventas
  - clima
  - feriado
  - tipo_feriado
- Fetching weather and holiday metadata to enrich the sales context
- Generating ML artifacts for store-level forecasting
- Exposing API endpoints for:
  - uploading files
  - listing and creating branches
  - retrieving metrics
  - generating 7-day sales predictions
  - fetching processed stores, products, and sales history

Main API routes:

- POST /load: upload a workbook and assign it to a store
- POST /predict: generate predictions for a store
- GET /metrics: fetch aggregate KPIs and business metrics
- GET /metrics/processed-data-summary: get summary information about imported data
- GET /metrics/processed-products-catalog: list processed products
- GET /metrics/processed-sales: retrieve sales records in a date window
- GET /metrics/sale-detail: retrieve the detail of a specific sale
- GET /api/sucursales: list stores
- POST /api/sucursales: create a store
- PUT /api/sucursales/{id}: update a store name
- PUT /api/sucursales/{id}/activo: enable or disable a store

The backend also sets up CORS to allow the frontend running locally on port 8080 to communicate safely with the API on port 8000.

## Frontend responsibilities

The frontend lives in the [front/](/Users/fedeb/OneDrive/Documentos/Vision-1/front) folder and is built with React, TypeScript, Vite, Tailwind CSS, and shadcn/ui components.

It is responsible for:

- Showing a dashboard for each store
- Letting the user switch between stores and date ranges
- Displaying business KPIs, charts, tables, and trend views
- Uploading Excel files to the backend
- Running and visualizing sales predictions
- Showing processed product catalogs and raw sales history
- Offering a clean workspace for sales analysis and store management

The main interface includes:

- Sidebar with stores and search
- Top bar with date-range filters and view selection
- Metrics dashboard with cards and charts
- Predictions tab with next-day or next-week forecasts
- Data tab for viewing imported sales information and product catalog

## End-to-end data flow

1. User selects a store in the frontend.
2. User uploads an Excel file from the dashboard.
3. The backend validates the file and reads the workbook.
4. Data is normalized and inserted into PostgreSQL.
5. The backend calls weather and public holiday APIs.
6. The ML job generates forecasting metadata for the store.
7. The frontend requests metrics and predictions through the API.
8. Charts and tables are rendered in the browser.

## Project structure

- [back/](/Users/fedeb/OneDrive/Documentos/Vision-1/back): FastAPI backend
- [front/](/Users/fedeb/OneDrive/Documentos/Vision-1/front): React frontend
- [docker-compose.yml](/Users/fedeb/OneDrive/Documentos/Vision-1/docker-compose.yml): local full-stack orchestration
- [DOCKER.md](/Users/fedeb/OneDrive/Documentos/Vision-1/DOCKER.md): Docker setup guide

## Running the project locally

### Option 1: Docker Compose

From the repository root:

```powershell
docker compose up --build
```

Then open:

- Frontend: http://localhost:8080
- Backend: http://localhost:8000
- pgAdmin: http://localhost:5050

### Option 2: Run backend manually

```powershell
cd back
py -3.11 -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

Create a `.env` file if needed with your PostgreSQL connection string:

```text
CREDENTIALS=postgresql://vision_user:vision_pass@localhost:5432/vision_db
```

Then start the API:

```powershell
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### Option 3: Run frontend manually

```powershell
cd front
npm install
npm run dev -- --host 0.0.0.0 --port 8080
```

## Summary

This project combines business data ingestion, analytics, and machine learning in one platform. The backend handles the heavy lifting: cleaning data, storing it, adding external context, and serving intelligence. The frontend focuses on presenting that information in a clean, interactive dashboard so users can make decisions based on sales performance and forecasts.
