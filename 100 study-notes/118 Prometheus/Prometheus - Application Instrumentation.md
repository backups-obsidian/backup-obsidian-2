---
created: 2024-01-08 08:41
updated: 2024-01-08 16:29
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - PromQL]] |
|-|

| Next: [[Prometheus - Service Discovery]] |
|-|

---
## Application Instrumentation
- If we want to collect metrics from our application we will have to instrument it.
- **The Prometheus client libraries provide an easy way to add instrumentation to our code in order to track and expose metrics for Prometheus**. They do the following:
	- Track metrics in the Prometheus expected format.
	- Expose metrics via `/metrics` path.
- Prometheus has official and unofficial third party client libraries.

### Instrumenting a Python API (Counter)
```python title:"example python flask api for instrumentation" fold
from flask import Flask

app = Flask(__name__)

@app.get("/cars")
def get_cars():
  return ["toyota", "honda", "mazda", "lexus" ]

if __name__ == "__main__":
  app.run (port='5001')
```

- We can install the client library in Python using `pip install prometheus_client`.

```python title:"initializing the counter metric" fold
from flask import Flask
from prometheus_client import Counter, start_http_server

# providing the name and description for the Counter metric
REQUESTS = Counter("http_requests_total", "Total number of requests")

app = Flask(__name__)

@app.get("/cars")
def get_cars():
  REQUESTS.inc() # increasing the counter
  return ["toyota", "honda", "mazda", "lexus" ]

if __name__ == "__main__":
  app.run (port='5001')
  start_http_server(8000) # start the metrics server on port 8000
```

- To get the metric of the application we would use: `curl localhost:8000`.
- The above method starts another http server to expose the endpoint. 
	- We can serve it from flask server itself on the `/metrics` endpoint using a flask specific implementation.

#### Using labels in instrumentation
```python title:"using labels with metrics" fold
REQUESTS = Counter('http_requests_total', 
				   'Total number of requests', 
				   labelnames=['path', 'method'])

@app get("/cars")
def get_cars():
  REQUESTS.labels('/cars', 'get').inc()
  return ["toyota", "honda", "mazda", "lexus"]

@app.post("/cars")
def create_cars():
  REQUESTS.labels('/cars', 'post').inc()
  return "Create Car"

@app.get("/boats")
def get_boats():
  REQUESTS.labels('/boats' 'get').inc()
  return ["boat1", "boat2"]

@app.post("/boats")
def create_boat():
  REQUESTS.labels('/boats', 'post').inc()
  return "Create Boat"
```

### Instrumenting a Python API (Histogram/Summary)

- **In this example we are adding a histogram metric to track latency/response time for each request**.

```python title:"histogram/summary API instrumentation" fold
# the client library will automatically try to generate the buckets but we can also specify it manually.
LATENCY = Histogram('request_latency_seconds', 
					'Request Latency',
					Labelnames=['path', 'method' ])

def before_request():
  request.start_time = time.time()

def after_request(response) :
  request_latency = time.time() - request.start_time
  LATENCY.labels(request.method, request.path).observe(request_latency)
  return response

if __name__ == "__main__':
  start_http_server(8000)
  # run something before and after every request in flask
  app.before_request(before_request)
  app.after_request(after_request)
```

### Instrumenting a Python API (Gauge)
- In the below example we will create a gauge metric that will *track the number of active requests getting processed*.

```python title:"gauge pythone API instrumentation" fold
IN_PROGRESS = Gauge('inprogress_requests',
					'Total number of requests in progress',
					labeLnames=['path', 'method'])

def before_request() :
  IN_PROGRESS.labels(request.method, request.path).inc()
  request.start_time = time.time()

def after_request(response):
  IN_PROGRESS.labels(request.method, request.path).dec()
  return response
```

- In the above example we are incrementing and decrementing the metric.
- *We also have a `set()` function if we want to set the metric to a specific value*.

## Best Practices
- **Naming convention to be followed: `library_name_unit_suffix`**.
	- Metrics should follow *snake_case* - lowercase with words separated by \_.
	- The *first word of the metric should be the application/library the metric is used for*.
		- Example: `postgres_`
	- The *name portion of the metric name should provide a description of what the metric is used for*. 
		- More than one word can be used.
		- Example: `postgres_queue_size`
	- *The unit should always be included in the metric name, so there's no second guessing which units its using*.
		- Make sure to *use unprefixed base units like seconds and bytes, and meters*. 
		- We don't want to use microseconds or kilobytes.
		- Example: `postgres_queue_size_bytes`.
	- *\_total, \_count, \_sum, and \_bucket suffixes are used by various metrics so its best to avoid adding those ourselves*.
		- *Only exception is to add \_total for counter metrics*.
- Example of good bad names:
	- `process_cpu_seconds`
	- `http_requests_total`
	- `redis_connection_errors`
	- `node_disk_read_bytes_total`
- Example of bad names:
	- `container_docker_restarts`: docker is the library so it should come first.
	- `http_requests_sum`: names should not have `_sum` at the end its misleading.
	- `nginx_disk_free_kilobytes`: units should have unprefixed base units.
	- `dotnet_queue_waiting_time`: no mention of units.