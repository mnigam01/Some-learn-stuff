1. some ways to use prometheus_client
```python
from prometheus_client import Counter, Histogram, generate_latest
REQUEST_COUNT = Counter("ml_requests_total", "Total ML Requests")
LATENCY = Histogram("ml_latency_seconds", "Request Latency")
```

2. Counter (only goes up)
first parameter is key and second is decription
Example uses:
Total requests
Total errors
Total logins
Total predictions
Total failures

Usage
REQUEST_COUNT.inc()
REQUEST_COUNT.inc(4.6)

3. Histogram
Measures distribution
with LATENCY.time():
    time.sleep(...) # track time of how long this block did

4. Gauge (like counter but goes up and down)
Examples:
Active users
Queue size
Memory usage
CPU usage
In-progress requests
g = Gauge('my_inprogress_requests', 'Description of gauge')
g.inc()      # Increment by 1
g.dec(10)    # Decrement by given value
g.set(4.2)   # Set to a given value

5. In ml context how to use it
```python
PREDICTIONS = Counter(
    "ml_predictions_total",
    "Predictions made",
    ["class"]   # LABEL
)

PREDICTIONS.labels(class="fraud").inc()
```

6. One more way to use Counter
```python
Counter("requests_total", "desc", ["method", "status"])
REQUESTS.labels(method="POST", status="200").inc()
```

7. how to write prometheus.yml
```yaml
global:
  scrape_interval: 15s

scrape_configs:

  - job_name: "ml-app"
    static_configs:
      - targets: ["ml-app:5000"]   # ml app is docker container name

  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]  # internal network alias

  - job_name: "node-exporter"
    static_configs:
      - targets: ["node-exporter:9100"]
```

8. Fastapi Dockerfile
```yaml
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "5000"]
```

9. docker-compose.yaml
```yaml
version: "3.8"

services:

  ml-app:
    build: .
    ports:
      - "5000:5000"

  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
```

10. sample app.py
```python
import random
import time
from fastapi import FastAPI, Response
from prometheus_client import Counter, Histogram, generate_latest

app = FastAPI()

REQUEST_COUNT = Counter("ml_requests_total", "Total ML Requests")
LATENCY = Histogram("ml_latency_seconds", "Request Latency")

@app.get("/")
def home():
    return {"message": "ML System Running"}

@app.post("/predict")
def predict():
    REQUEST_COUNT.inc()

    with LATENCY.time():
        time.sleep(random.uniform(0.1, 0.5))

    return {
        "prediction": random.choice(["safe", "fraud"]),
        "confidence": random.uniform(0.5, 0.99)
    }

@app.get("/metrics")
def metrics():
    return Response(generate_latest(), media_type="text/plain")
```

11. start everything
docker compose up

12. check endpoints

13. open grafana and prometheus dashboard using localhost:port number

14. in grafana data connection use http://prometheus:9090 not local host because it is running in 
two separate container in docker so you need to call using service name.