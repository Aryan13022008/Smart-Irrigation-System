# Smart Irrigation System

An ESP32-connected smart agriculture prototype combining sensor monitoring, weather context, irrigation decision support, machine-learning experiments, and image-based weed detection. The project also includes a separately trained 30-minute soil-dryness research model contributed from the second repository.

## Features

- Streamlit dashboard for sensor status, irrigation monitoring, weather information, and model inspection.
- ESP32 serial telemetry reader and Arduino sensor-test sketch.
- SQLite storage for sensor history and probe calibration.
- Irrigation classification experiments using Random Forest / gradient-boosting and sequence models (LSTM, TCN, Transformer), where the corresponding artifacts are available.
- YOLO-based weed-detection pipeline, including dataset preparation, training, evaluation, and inference utilities.
- Experimental live moisture trend / sequence forecasting utilities with explicit data-readiness checks.
- Separate research page for the 30-minute future soil-dryness classifier from the second repository.

## Repository layout

```text
Smart-Irrigation-System/
├── app.py                         # Main Streamlit application
├── training/                      # Inference, models, training utilities
│   ├── models/                    # Model artifacts and evaluation metadata
│   └── experimental/              # Forecasting and sensor-history modules
├── weed_detection/                # YOLO weed detection pipeline
├── esp32/                         # ESP32 sensor test sketch
├── data/                          # Project datasets and metadata
├── research/dryness_forecasting/  # Unique second-repository research work
├── tests/                         # Project tests and diagnostics
├── docs/                          # Technical notes
├── requirements.txt
└── README.md
```

## Requirements

- Python 3.10–3.12 (recommended)
- pip
- PostgreSQL is **not** required; the local telemetry store uses SQLite.
- ESP32 board and configured sensors for live hardware functionality

## Setup

```bash
git clone <YOUR_REPOSITORY_URL>
cd Smart-Irrigation-System
python -m venv .venv
source .venv/bin/activate        # macOS / Linux
# .venv\Scripts\activate       # Windows
pip install -r requirements.txt
```

Run the dashboard:

```bash
streamlit run app.py
```

The dashboard can open without an ESP32 connected, but hardware-dependent sections will report unavailable readings until the device and serial configuration are set up. The default Mac serial port is `/dev/cu.usbserial-0001`; override it with `export ESP32_SERIAL_PORT=/your/serial/port` before launching Streamlit. The SQLite database is created locally at runtime; it is intentionally not committed.

## ESP32 hardware

The archived hardware configuration notes and sketch are in `HARDWARE_INTEGRATION_NOTE.txt` and `esp32/sensor_test/sensor_test.ino`. The previously used wiring was DHT22 on GPIO 4, soil probes on GPIO 32 and 33, water-level sensor on GPIO 34, and rain sensor on GPIO 27. Verify the actual wiring and serial port before use. Do not enable pump/actuator control without independent hardware safety checks.

## ML models and evaluation

Model artifacts and their metrics are stored under `training/models/`. Read the associated JSON metadata before interpreting reported scores: models use different feature schemas, datasets, and evaluation procedures. A metric from one model is not directly comparable to another unless the split and target definitions match. Model files should only be used with the matching inference code and feature schema.

## Separate 30-minute soil-dryness research model

The second repository contained a distinct model bundle and a 48,523-row combined dataset. Its features are `soil_moisture_1`, `soil_moisture_2` (decimal fractions), `soil_temperature` (°C), and `rainfall` (mm per 5-minute interval). The target labels whether future moisture, approximately 30 minutes later, is at or below the training-period 20th percentile. This is a provisional research label, not a validated crop-specific irrigation threshold.

The dashboard exposes this under **Soil Dryness Forecast (Research)**. It asks for values in the training dataset's units. It does not convert ESP32 raw ADC readings or feed live telemetry into this model, because the units and calibration are not compatible. Reproduce the model with the scripts in `research/dryness_forecasting/src/`; the source spreadsheets and combined CSV are included.

## Testing and checks

Run the Python syntax check:

```bash
python -m compileall -q app.py training weed_detection research
```

Run the included tests (some hardware/API tests require devices, services, or optional dependencies):

```bash
python -m pytest tests -q
```

## Configuration and privacy

- Do not commit `.env` files, API keys, local databases, serial logs, or personal machine paths.
- The project uses weather API requests in the dashboard; check the relevant source before configuring external services.
- Uploaded images and datasets should be checked for licensing and personal information before publication.

## Project status and limitations

- Weed detection has a separate model and pipeline.
- Crop-disease detection code was referenced by the older dashboard, but a complete disease-detection module was not present in either supplied archive; it is not represented as a working feature here.
- Some forecasting experiments are research-only or require a sufficient continuous sensor history.
- No claim of automatic pump actuation is made by this cleaned repository.

## Acknowledgement

This repository consolidates selected useful work from two earlier Smart Irrigation project archives. It preserves distinct contributions while avoiding duplicate dashboard copies, Python cache files, and local runtime database snapshots.
