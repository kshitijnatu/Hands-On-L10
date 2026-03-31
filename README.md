# Hands-On L10: Spark Streaming + MLlib

This project demonstrates two PySpark Structured Streaming workflows using a socket data source and MLlib regression models:

- `task4.py`: real-time fare prediction per trip (`distance_km -> fare_amount`)
- `task5.py`: real-time trend prediction (windowed average fare by time features)

Data is generated continuously by `data_generator.py`.

## Project Files

- `data_generator.py` - emits JSON ride events over a local socket (`localhost:9999`)
- `task4.py` - trains/loads `models/fare_model`, predicts fare + deviation
- `task5.py` - trains/loads `models/fare_trend_model_v2`, predicts next window average fare
- `training-dataset.csv` - offline training dataset used by both tasks

## Prerequisites

- Python 3.11+ (3.11 recommended for best PySpark compatibility)
- Java installed and configured (`JAVA_HOME`)
- `pip`

## Setup

From the project root:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install pyspark faker
```

Verify:

```bash
python -c "import pyspark; print('PySpark OK')"
java -version
```

## How to Run

Run in two terminals from the same project directory.

### 1) Start stream generator (Terminal A)

```bash
source .venv/bin/activate
python data_generator.py
```

### 2) Start Spark job (Terminal B)

For Task 4:

```bash
source .venv/bin/activate
python task4.py
```

For Task 5:

```bash
source .venv/bin/activate
python task5.py
```

The first run trains and saves a model; later runs load the saved model automatically.

## Output

Both tasks currently write to the console stream sink.

If you want to save all terminal output to a file:

```bash
python task4.py > output.txt
```

## Model Artifacts

- Task 4 model path: `models/fare_model`
- Task 5 model path: `models/fare_trend_model_v2`

If a model path gets corrupted/incomplete from interrupted runs, delete that model folder and rerun to retrain.

## Common Issues

- `Import ... could not be resolved`
  - Ensure the correct imports from `pyspark.ml.regression` and `pyspark.ml.feature`.
- `PATH_NOT_FOUND ... /metadata`
  - Model directory does not contain a complete saved model. Retrain and ensure save call executes.
- Java/Spark startup errors (`ClassNotFoundException`, JVM init issues)
  - Use a supported JDK (commonly Java 17) and confirm `JAVA_HOME` points to it.
- Socket stream seems stuck
  - Start `data_generator.py` first, then start `task4.py`/`task5.py`.
