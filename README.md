# pgbase

pgbase is a fork of [PocketBase](https://pocketbase.io), using PostgreSQL instead of SQLite.

Everything else — features, API, Admin UI, documentation — works the same as PocketBase.
The only difference: PostgreSQL powers the backend.

Based on [PocketBase](https://pocketbase.io) version 0.27.1

## Quickstart

```bash
git clone git@github.com:thewandererbg/pgbase.git
cd examples/base
GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o pgbase
./pgbase serve
```

Set your database connection:

```bash
export PB_DATA_URI="postgres://user:pass@localhost:5432/pbdata?sslmode=disable&default_query_exec_mode=simple_protocol"
export PB_AUX_URI="postgres://user:pass@localhost:5432/pbaux?sslmode=disable&default_query_exec_mode=simple_protocol"
```

## Docker

### Using pre-built image from Docker Hub

```bash
docker run -d \
  --name pgbase \
  -p 8090:8090 \
  -e PB_DATA_URI="postgres://user:pass@host:5432/pbdata?sslmode=disable&default_query_exec_mode=simple_protocol" \
  -e PB_AUX_URI="postgres://user:pass@host:5432/pbaux?sslmode=disable&default_query_exec_mode=simple_protocol" \
  -v pgbase_data:/pb/pb_data \
  teammason2024/pgbase:latest
```

Or with Docker Compose:

```yaml
services:
  pgbase:
    image: teammason2024/pgbase:latest
    container_name: pgbase
    ports:
      - 8090:8090
    environment:
      - PB_DATA_URI=postgres://user:pass@host:5432/pbdata?sslmode=disable&default_query_exec_mode=simple_protocol
      - PB_AUX_URI=postgres://user:pass@host:5432/pbaux?sslmode=disable&default_query_exec_mode=simple_protocol
    volumes:
      - pgbase_data:/pb/pb_data

volumes:
  pgbase_data:
```

Access the Admin UI at http://localhost:8090/_/.

### Building locally with Docker

```bash
git clone git@github.com:thewandererbg/pgbase.git
cd pgbase/docker
docker compose up -d
```

## Local Development Setup

### Prerequisites

- Go 1.24+
- PostgreSQL 15+
- Node.js 20+ (for frontend/admin UI development)

### Clone and setup

```bash
git clone git@github.com:thewandererbg/pgbase.git
cd pgbase
```

### Create PostgreSQL databases

```bash
createdb pbdata
createdb pbaux
```

### Set environment variables

```bash
export PB_DATA_URI="postgres://user:pass@localhost:5432/pbdata?sslmode=disable&default_query_exec_mode=simple_protocol"
export PB_AUX_URI="postgres://user:pass@localhost:5432/pbaux?sslmode=disable&default_query_exec_mode=simple_protocol"
```

### Run the application

```bash
cd examples/base
go run main.go serve
```

### Run with hot reload (using air)

```bash
# Install air
go install github.com/air-verse/air@latest

# Run with hot reload
air -c .air.toml
```

### Run tests

```bash
# Unit tests
go test ./...

# Integration tests (require running PostgreSQL)
go test -tags=integration ./...
```

### Project Structure

```
pgbase/
├── core/              # Core application logic
├── apis/              # HTTP API handlers
├── tools/             # Utility functions
├── plugins/           # Built-in plugins (jsvm, migratecmd, ghupdate)
├── examples/
│   └── base/          # Example application entry point
├── docker/            # Docker configuration
└── migrate/           # Database migrations
```

### Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Run tests (`go test ./...`)
5. Ensure code passes linting (`golangci-lint run`)
6. Commit your changes (`git commit -m 'Add amazing feature'`)
7. Push to the branch (`git push origin feature/amazing-feature`)
8. Open a Pull Request

## Docs

Use the [PocketBase documentation](https://pocketbase.io/docs/) — just remember you're running on PostgreSQL.

### Differences with PocketBase

pgbase follows PocketBase behavior and APIs as closely as possible, with the following differences:

- Uses PostgreSQL instead of SQLite for all data storage
- Backup feature is not supported
- Cascade delete behavior differs from PocketBase
- Optional multi-instance support via PostgreSQL `LISTEN / NOTIFY`

#### Multi-instance support

pgbase can run multiple instances connected to the same PostgreSQL database.

When `IsPubSubEnabled` config is enabled:
- Collection schema changes are propagated to all instances
- Settings changes are propagated to all instances
- Each instance reloads its local cache automatically

Limitations:
- Realtime API events are **not propagated** across instances

When `IsPubSubEnabled` config is disabled:
- All cache reloads are local to the current instance only


## License

MIT. Forked from [PocketBase](https://pocketbase.io).