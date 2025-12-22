# otel-demo-lite

A minimal multi-language microservices demo for testing OpenTelemetry instrumentation. Simulates an e-commerce backend with services written in Go, Node.js, and Python—all wired up to emit traces, metrics, and logs via OTLP.

## What's in here

```
go/           → checkout, cart, product catalog, shipping, currency, accounting, fraud detection
javascript/   → frontend gateway, payment, ads, email, browser simulator
python/       → recommendations, shipping quotes
```

The services talk to each other over HTTP. A browser simulator generates fake traffic so you don't have to click around manually.

## Running it

### With Docker (recommended)

```bash
docker-compose up
```

This spins up:
- All the microservices in one container
- An OpenTelemetry Collector
- Redis for cart storage

### Without Docker

Make sure you have Go 1.21+, Node.js 18+, and Python 3.11+ installed.

```bash
./run.sh
```

You'll need an OTel Collector running on `localhost:4317` to receive telemetry.

## Services

| Service | Port | What it does |
|---------|------|--------------|
| Frontend | 8080 | API gateway, routes requests |
| Payment | 8081 | Processes charges (5% failure rate for realism) |
| Shipping | 8082 | Gets quotes, ships orders |
| Checkout | 8083 | Orchestrates the purchase flow |
| Cart | 8084 | Redis-backed shopping cart |
| Product Catalog | 8085 | Lists products, search |
| Recommendation | 8086 | Suggests products |
| Ad | 8087 | Serves ads |
| Email | 8088 | Order confirmations |
| Currency | 8089 | Converts between currencies |
| Browser Simulator | 8090 | Generates load, records Web Vitals |
| Accounting | 8091 | Consumes orders from Kafka |
| Fraud Detection | 8092 | Scans orders (2% detection rate) |
| Quote | 8094 | Calculates shipping costs |

## Telemetry

All services export to the OTel Collector via OTLP (gRPC on 4317, HTTP on 4318). The collector config batches everything and forwards to wherever you point it—by default it's set up for SigNoz but you can swap in Jaeger, Tempo, or anything else.

Each service instruments:
- **Traces**: HTTP spans, database calls, cross-service propagation
- **Metrics**: Request counts, latencies, business metrics (order totals, quote amounts)
- **Logs**: Structured logs with trace context

## Load testing

The browser simulator runs automatically and generates traffic. To crank it up:

```bash
COUNT=100 docker-compose up
```

Or hit the frontend directly:

```bash
node load-test.js
```

## Configuration

Copy `.env.example` to `.env` and configure:

```bash
cp .env.example .env
```

Or pass environment variables directly:

```bash
OTLP_ENDPOINT=your-host:4317 OTLP_INSECURE=true docker-compose up
```

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `OTLP_ENDPOINT` | `ingest.us.signoz.cloud:443` | OTLP endpoint. For self-hosted use `your-host:4317` |
| `SIGNOZ_INGESTION_KEY` | (empty) | SigNoz ingestion key (not needed for self-hosted) |
| `OTLP_INSECURE` | `false` | Set `true` for self-hosted without TLS |
| `OTLP_INSECURE_SKIP_VERIFY` | `false` | Set `true` to skip TLS cert verification |
| `COUNT` | `5` | Number of simulated orders per cycle |

### Load Test Mode

Set `COUNT=0` to run services without generating traffic—useful for external load testing:

```bash
COUNT=0 docker-compose up
```

Services will run indefinitely and expose endpoints at `http://localhost:8080/api/*`.

The collector uses `otlp` exporter for gRPC (port 4317). Edit `otel-collector-config.yaml` to point to your backend.
