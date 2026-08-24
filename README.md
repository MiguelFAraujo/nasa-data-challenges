# NASA Data Challenges Lab

IDEs: Multi-IDE (CLion, DataGrip, GoLand, IntelliJ, PhpStorm, PyCharm, Rider, RubyMine, RustRover, WebStorm)
Stack: Python, Go, Rust, TypeScript, Java, C++, SQL - processamento de dados reais NASA
Integracao Lab: Ollama (analise), n8n (pipelines), MariaDB/PostgreSQL/ClickHouse, Redis, Prometheus/Grafana, Tailscale

## Visao Geral

Projetos multi-linguagem processando datasets reais NASA para portfolio tecnico:
- FIRMS: Deteccao de incendios em tempo real (satellite MODIS/VIIRS)
- NEO: Asteroides proximos da Terra (JPL CNEOS)
- Mars Rover: Fotos Curiosity/Perseverance (NASA PDS)
- Climate: Dados temperatura/CO2 (GISS, GES DISC)
- Exoplanets: Kepler/TESS (NASA Exoplanet Archive)

Cada desafio implementado na linguagem/IDE mais adequada.

## Arquitetura

```
Multi-IDE (10 IDEs JetBrains)
        |
        v
NASA APIs -> ETL (n8n) -> Processamento (Python/Go/Rust/TS/Java/C++) -> Analytics -> Dashboard
        |
        v
Lab Stack: MariaDB, PostgreSQL, ClickHouse, Redis, Prometheus, Grafana, Ollama, n8n
```

## Datasets NASA Utilizados

| Dataset | API/Source | Frequencia | Volume | IDE/Linguagem |
|---------|------------|------------|--------|---------------|
| FIRMS Fire | NASA FIRMS API | Near real-time | 10k+/dia | PyCharm (Python) |
| NEO Asteroids | JPL CNEOS API | Diario | 30k+ objetos | GoLand (Go) |
| Mars Photos | NASA PDS / Mars API | Diario | 500k+ imagens | RustRover (Rust) |
| Climate Data | GISS / GES DISC | Mensal | 100M+ registros | DataGrip (SQL) + CLion (C++) |
| Exoplanets | NASA Exoplanet Archive | Atualizacao periodica | 5k+ confirmados | IntelliJ (Java) + WebStorm (TS) |

## Inicio Rapido

```bash
# Subir stack do lab
docker-compose -f docker-compose.lab.yml up -d

# Configurar NASA API key (gratuita em api.nasa.gov)
cp .env.lab .env
# Editar .env com NASA_API_KEY

# Executar pipelines n8n
# Workflows: firms_etl, neo_etl, mars_etl, climate_etl, exoplanet_etl

# Dashboards Grafana
# http://lab-ip:3030/d/nasa-firms, nasa-neo, nasa-mars, nasa-climate, nasa-exoplanets
```

## Benchmarks Lab-Testados

| Pipeline | Linguagem | Throughput | Latencia | Ferramenta |
|----------|-----------|------------|----------|------------|
| FIRMS ETL | Python | 5k records/s | 200ms | PyCharm Profiler |
| NEO ETL | Go | 50k records/s | 50ms | GoLand pprof |
| Mars Download | Rust | 100 MB/s | - | RustRover criterion |
| Climate SQL | SQL/C++ | 1M rows/s | 10ms | DataGrip + CLion |
| Exoplanet API | Java/TS | 10k req/s | 30ms | IntelliJ + WebStorm |

> **Hardware de teste**: Daten DQ170UP (Intel Core i5-7600T 2.8GHz, 15GB RAM, Ubuntu 24.04 LTS)
> **IDEs**: 10x JetBrains 2026.2 | **OS**: Ubuntu 24.04 LTS

## Estrutura do Projeto

```
nasa-data-challenges/
├── .idea/                      # Configs multi-IDE (shared)
├── firms-fire-detection/       # PyCharm: Python + Pandas + Folium
│   ├── src/                    # ETL, analysis, visualization
│   ├── notebooks/              # Jupyter exploration
│   └── tests/
├── neo-asteroid-tracker/       # GoLand: Go + gRPC + Prometheus
│   ├── cmd/                    # CLI, server
│   ├── internal/               # NASA API client, orbit calc
│   └── benches/
├── mars-rover-imagery/         # RustRover: Rust + WASM + WebGL
│   ├── backend/                # Axum + NASA PDS client
│   ├── frontend/               # Leptos WASM + WebGL viewer
│   └── benches/
├── climate-analytics/          # DataGrip + CLion: SQL + C++
│   ├── sql/                    # ClickHouse + PostgreSQL queries
│   ├── cpp/                    # High-perf analytics engine
│   └── benchmarks/
├── exoplanet-explorer/         # IntelliJ + WebStorm: Java + TS
│   ├── java-backend/           # Spring Boot + NASA API
│   ├── ts-frontend/            # Next.js + D3.js visualization
│   └── tests/
├── shared/
│   ├── n8n-workflows/          # 5 ETL workflows
│   ├── grafana-dashboards/     # 5 dashboards JSON
│   └── ollama-prompts/         # AI analysis prompts
├── scripts/
│   ├── run_all_pipelines.sh    # Orquestra todos
│   ├── benchmark_all.sh        # Benchmarks comparativos
│   └── ai_analysis.py          # Ollama analisa resultados
├── docker-compose.lab.yml      # MariaDB, PG, ClickHouse, Redis, Prometheus, Grafana, Ollama
├── .github/workflows/          # CI/CD multi-linguagem
└── docs/                       # Arquitetura, ADRs, benchmarks, NASA API docs
```

## Integracao Lab

### n8n ETL Workflows
```json
// 5 workflows: firms, neo, mars, climate, exoplanet
// Schedule -> NASA API -> Transform -> Load (ClickHouse/PG) -> Metrics -> Alert
```

### Ollama AI Analysis
```python
# scripts/ai_analysis.py
prompt = f"Analyze this NASA data anomaly: {anomaly_data}. Provide scientific interpretation."
review = ollama.chat(model='llama3.2:latest', messages=[{'role': 'user', 'content': prompt}])
```

### Grafana Dashboards
- FIRMS: Fire hotspots map, temporal trends, confidence distribution
- NEO: Orbit visualization, close approaches, risk assessment (Torino scale)
- Mars: Rover traverse map, image gallery, sol timeline
- Climate: Temperature anomalies, CO2 trends, regional heatmaps
- Exoplanets: Habitable zone, discovery timeline, stellar properties

## Testes

```bash
# Todos os pipelines
./scripts/run_all_pipelines.sh

# Benchmarks comparativos
./scripts/benchmark_all.sh

# Testes por linguagem
cd firms-fire-detection && poetry run pytest
cd neo-asteroid-tracker && go test ./...
cd mars-rover-imagery && cargo test --workspace
cd climate-analytics && ./run_benchmarks.sh
cd exoplanet-explorer/java-backend && ./mvnw test
cd exoplanet-explorer/ts-frontend && npm test
```

## Pipeline CI/CD

```yaml
# .github/workflows/ci.yml
- Python: pytest + black + mypy (FIRMS)
- Go: test + bench + lint (NEO)
- Rust: test + clippy + miri + bench (Mars)
- SQL/C++: query validation + benchmark (Climate)
- Java: test + native + benchmark (Exoplanet backend)
- TypeScript: test + lint + e2e (Exoplanet frontend)
- n8n: workflow validation
- Deploy: n8n -> lab k3s
- AI Review: Ollama no PR
```

---

Desenvolvido com 10 IDEs JetBrains 2026.2 + Educational Pack BD24G146N7
Lab-tested on IDT-Lab (Daten DQ170UP + MariaDB + PostgreSQL + ClickHouse + Redis + Prometheus + Grafana + Ollama + n8n + Tailscale)
Dados: NASA FIRMS, JPL CNEOS, NASA PDS, GISS, NASA Exoplanet Archive (APIs publicas gratuitas)
Parte do JetBrains IDE Portfolio
