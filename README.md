# otel-demo-lite

Minimal multi-language microservices demo for OpenTelemetry. Simulates an e-commerce backend with Go, Node.js, and Python services emitting traces, metrics, and logs via OTLP.

## Quick Start

```bash
docker-compose up
```

This starts all services, an OTel Collector, and Redis. Point it at your backend:

```bash
OTLP_ENDPOINT=your-host:4317 OTLP_INSECURE=true docker-compose up
```

For SigNoz Cloud:

```bash
OTLP_ENDPOINT=ingest.us.signoz.cloud:443 SIGNOZ_INGESTION_KEY=your-key docker-compose up
```

## Architecture

```
Browser Simulator (JS)
  → Frontend (JS)
      → Product Catalog (Go/SQLite)
      → Cart (Go/Redis)
      → Recommendation (Python)
      → Checkout (Go)
          → Payment (JS)
          → Shipping (Go) → Quote (Python)
          → Email (JS)
          → Accounting (Go)
          → Fraud Detection (Go)
```

## Services

| Service | Language | Port |
|---------|----------|------|
| Frontend | JS | 8080 |
| Payment | JS | 8081 |
| Shipping | Go | 8082 |
| Checkout | Go | 8083 |
| Cart | Go | 8084 |
| Product Catalog | Go | 8085 |
| Recommendation | Python | 8086 |
| Ad | JS | 8087 |
| Email | JS | 8088 |
| Currency | Go | 8089 |
| Browser Simulator | JS | 8090 |
| Accounting | Go | 8091 |
| Fraud Detection | Go | 8092 |
| Quote | Python | 8094 |

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `OTLP_ENDPOINT` | `ingest.us.signoz.cloud:443` | OTel Collector endpoint |
| `SIGNOZ_INGESTION_KEY` | - | SigNoz Cloud ingestion key |
| `OTLP_INSECURE` | `false` | Use plain gRPC (no TLS) |
| `RPS` | `5` | Requests per second (0 = server-only mode) |

## Local Development

Requires Go 1.21+, Node.js 18+, Python 3.11+.

```bash
./run.sh
```

Expects an OTel Collector on `localhost:4317`.

## License

Apache 2.0
