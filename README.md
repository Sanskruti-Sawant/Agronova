# AgroNova 🌱

**AI-powered crop and fertilizer recommendation platform**

AgroNova is a web application designed to support data-informed agricultural decisions. It combines soil-image classification, machine-learning models, and weather/location information to generate crop and fertilizer recommendations through a FastAPI backend and a responsive web frontend.

> **Important:** AgroNova is an educational decision-support project. Its predictions are estimates based on the datasets and model assumptions used by the application. They are not a substitute for laboratory soil testing or advice from a qualified agricultural expert.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [How It Works](#how-it-works)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Running with Docker](#running-with-docker)
- [API](#api)
- [Machine-Learning Models](#machine-learning-models)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Limitations and Responsible Use](#limitations-and-responsible-use)
- [Future Improvements](#future-improvements)
- [Contributing](#contributing)
- [License](#license)

## Overview

Choosing a suitable crop and fertilizer depends on several factors, including soil characteristics, nutrient levels, and local environmental conditions. AgroNova brings several of these inputs together in one workflow.

The current application includes:

1. A soil-image classification workflow.
2. A crop recommendation model that returns likely crop options.
3. A fertilizer recommendation model.
4. Weather and location utilities powered by the OpenWeather API.
5. A browser-based frontend for accessing the application's pages and prediction workflow.

## Key Features

- **Soil classification:** Classifies an uploaded soil image into one of the supported soil categories using a K-Nearest Neighbors (KNN) model.
- **Crop recommendation:** Uses a Random Forest-based classifier to produce crop recommendations from agricultural input features.
- **Top crop suggestions:** The prediction workflow can return the three highest-ranked crop options.
- **Fertilizer recommendation:** Uses a Random Forest model and the fertilizer dataset to suggest a fertilizer based on the available input features.
- **Weather integration:** Retrieves weather information using the OpenWeather API.
- **Location utilities:** Includes geocoding support for location-aware workflows.
- **Web interface:** HTML, CSS, and vanilla JavaScript frontend.
- **Container support:** Backend can be run using Docker and Docker Compose.

## How It Works

The main prediction workflow is:

1. The user opens the prediction page and provides the requested information, including a soil image and location.
2. The frontend sends the information to the FastAPI backend.
3. The backend preprocesses the soil image and uses the KNN model to classify the soil category.
4. The crop model processes the available agricultural and environmental features and returns likely crop options.
5. The fertilizer model uses the available soil, crop, and nutrient features to generate a fertilizer suggestion.
6. The frontend displays the prediction response.

Weather and geocoding are exposed through separate API routes. Exact inputs and fallback behavior are defined by the current backend implementation.

## Technology Stack

| Layer | Technologies |
|---|---|
| Frontend | HTML5, CSS3, JavaScript (Vanilla), Fetch API |
| Backend | Python, FastAPI, Uvicorn |
| Machine Learning | scikit-learn, KNN, Random Forest, calibrated classification |
| Data Processing | NumPy, Pandas |
| Image Processing | Pillow |
| External API | OpenWeather API |
| Deployment | Docker, Docker Compose |

## Project Structure

The repository is organized approximately as follows:

```text
Agronova/
├── frontend/
│   ├── index.html
│   ├── predict.html
│   ├── predict.js
│   ├── style.css
│   ├── predict.css
│   ├── about.html
│   ├── guide.html
│   ├── pests-diseases.html
│   ├── sustainable.html
│   ├── techniques.html
│   ├── support.html
│   └── images/
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   └── routes.py
│   │   ├── services/
│   │   │   ├── ml_utils.py
│   │   │   └── soil.py
│   │   ├── globals.py
│   │   ├── main.py
│   │   └── models.py
│   ├── data/
│   │   ├── Crop_Recommendation.csv
│   │   ├── Fertilizer Prediction.csv
│   │   └── soil_dataset/
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── requirements.txt
│
└── README.md
```

The soil image dataset is expected under `backend/data/soil_dataset/`, organized into class folders such as `alluvial`, `black`, `clay`, `loamy`, `red`, and `sandy`.

The backend currently loads the datasets and trains the models during application startup. Keep the expected dataset files and folder structure in place.

## Getting Started

### Prerequisites

Install the following before running the project locally:

- Python 3.10 (the Docker image uses Python 3.10)
- pip
- Git
- A valid OpenWeather API key
- A modern web browser

### 1. Clone the repository

```bash
git clone https://github.com/Sanskruti-Sawant/Agronova.git
cd Agronova
```

### 2. Configure the backend environment

Move into the backend directory:

```bash
cd backend
```

Create and activate a virtual environment.

**Windows PowerShell**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install the Python dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Set the API key

The backend reads the OpenWeather credential from the `OPENWEATHER_API_KEY` environment variable.

Set it in your terminal before starting the API.

**Windows PowerShell**

```powershell
$env:OPENWEATHER_API_KEY="YOUR_OPENWEATHER_API_KEY"
```

**macOS / Linux**

```bash
export OPENWEATHER_API_KEY="YOUR_OPENWEATHER_API_KEY"
```

Replace the placeholder with your own key. Do not commit real credentials to Git. If you use a local `.env` file, keep it private and make sure it is ignored by Git.

### 4. Start the backend

Run this command from the `backend/` directory:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

When the server starts, it loads the datasets and trains the soil, crop, and fertilizer models. The first startup may take longer than subsequent API requests because training occurs during startup.

The API will be available at:

- API base URL: http://localhost:8000
- Interactive Swagger documentation: http://localhost:8000/docs
- ReDoc documentation: http://localhost:8000/redoc
- Health check: http://localhost:8000/health

### 5. Run the frontend

Open the `frontend/` folder in your preferred editor and serve the HTML files using a local static development server (for example, the VS Code Live Server extension).

Open the frontend's `index.html` through that server. If the frontend is served from a different origin or port, make sure its API request URL points to the running backend and that the backend CORS configuration permits that frontend origin.

## Running with Docker

Docker provides a convenient way to run the backend in a container.

Make sure Docker Desktop (or Docker Engine with the Compose plugin) is installed and running. Configure the required environment variable securely for the Compose service before starting it.

From the `backend/` directory, run:

```bash
docker compose up --build
```

The API is exposed on port `8000`:

- http://localhost:8000
- http://localhost:8000/docs

To stop the containers:

```bash
docker compose down
```

The current Compose configuration mounts the backend application and data directories into the container. Keep the dataset files available locally so the application can train its models.

## API

The FastAPI application is mounted under the `/api` prefix. The current routes include:

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/health` | Check whether the API is running |
| GET | `/api/geocode` | Location/geocoding utility |
| GET | `/api/weather` | Retrieve weather information |
| POST | `/api/predict/soil` | Run the soil, crop, and fertilizer prediction workflow |

For exact query parameters, form fields, request formats, and response schemas, start the backend and open **[Swagger UI](http://localhost:8000/docs)**. The interactive API documentation is the source of truth for the current implementation.

## Machine-Learning Models

| Model | Algorithm | Purpose |
|---|---|---|
| Soil classifier | K-Nearest Neighbors (KNN) | Predicts a soil class from a preprocessed soil image |
| Crop recommender | Random Forest classifier with calibration | Ranks suitable crop options from the available input features |
| Fertilizer recommender | Random Forest classifier | Predicts a fertilizer category from the available input features |

### Soil image preprocessing

The current soil-image service uses Pillow to convert the image to grayscale, resize it to 64 × 64 pixels, flatten the pixel values, and scale them to the range 0–1 before classification.

### Model training

Models are trained when the FastAPI application starts. Training data is read from the CSV files and soil-image dataset in `backend/data/`. Changes to the datasets or preprocessing pipeline may change the resulting predictions.

## Configuration

| Variable | Purpose |
|---|---|
| `OPENWEATHER_API_KEY` | API key used for OpenWeather-backed weather and location functionality |
| `SOIL_MODEL_URL` | Optional model URL setting present in the environment template |
| `CROP_MODEL_URL` | Optional model URL setting present in the environment template |
| `FERT_MODEL_URL` | Optional model URL setting present in the environment template |

The current startup code trains the models from the local datasets. The model URL variables are not required for that local training workflow unless the implementation is changed to load externally hosted model artifacts.

## Troubleshooting

**The API fails during startup**

- Confirm that you are running commands from the `backend/` directory.
- Confirm that all Python dependencies installed successfully.
- Check that `Crop_Recommendation.csv` and `Fertilizer Prediction.csv` exist in `backend/data/`.
- Confirm that the soil image dataset exists and contains the expected class folders.
- Read the terminal output for the specific model-training or data-loading error.

**Weather requests fail**

- Confirm that `OPENWEATHER_API_KEY` is set in the environment used by the backend.
- Check that the key is valid and that the OpenWeather service is reachable.

**The frontend cannot reach the backend**

- Confirm that the API is running at the expected host and port.
- Check the API base URL used by the frontend JavaScript.
- Check browser developer tools for network and CORS errors.

**Docker cannot find data or configuration**

- Run `docker compose` from the `backend/` directory.
- Confirm that the local data directory and required environment configuration are available to the Compose service.

## Limitations and Responsible Use

- Model quality depends on the coverage, correctness, and representativeness of the training datasets.
- A soil image alone cannot provide a complete laboratory assessment of soil health or nutrient composition.
- Weather-derived values and model defaults are not equivalent to direct field measurements.
- Fertilizer predictions should be verified against soil-test results, crop requirements, local agricultural guidance, and product instructions.
- Do not use the application as the sole basis for high-impact agricultural or financial decisions.

## Future Improvements

Potential areas for further development include:

- Add model evaluation reports with accuracy, precision, recall, F1-score, and confusion matrices.
- Validate all user inputs and provide clearer error messages.
- Use measured soil parameters and reliable field data rather than inferred substitutes.
- Improve model validation and prevent data leakage during preprocessing and calibration.
- Add automated tests for API routes and prediction services.
- Restrict CORS to trusted frontend origins before production deployment.
- Add model versioning and a reproducible training pipeline.
- Improve accessibility, responsive behavior, and frontend-backend configuration.
- Provide explainability information for crop and fertilizer predictions.

## Contributing

Contributions, bug reports, and suggestions are welcome.

1. Fork the repository.
2. Create a feature branch: `git checkout -b feature/your-feature`.
3. Commit your changes: `git commit -m "Add your feature"`.
4. Push the branch: `git push origin feature/your-feature`.
5. Open a pull request describing your changes.

Please avoid committing API keys, local environment files, virtual environments, cache directories, or other generated artifacts.
