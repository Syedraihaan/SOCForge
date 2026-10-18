# SOCForge

# SOCForge — Security Telemetry & Attack Simulation Platform

**SOCForge** is an open-source security telemetry generation and attack simulation project designed for SOC training, detection engineering, and SIEM rule evaluation in controlled environments.

It aims to generate synthetic enterprise activity and security events, associate simulated attack behavior with MITRE ATT&CK techniques, and provide structured telemetry for security analysis. The project is intended to help security practitioners experiment with detection logic and investigate attack scenarios without requiring a production enterprise environment.

> **Project status:** Under development. Feature availability, test coverage, and integration support should be verified against the current implementation. SOCForge generates simulated telemetry; it does not replace real-world security monitoring or incident response systems.

## Table of Contents

- [Objectives](#objectives)
- [Key Features](#key-features)
- [Attack Scenarios](#attack-scenarios)
- [Technology and Architecture](#technology-and-architecture)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Running with Docker](#running-with-docker)
- [API Documentation](#api-documentation)
- [Testing](#testing)
- [Example Workflow](#example-workflow)
- [Security and Limitations](#security-and-limitations)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

## Objectives

SOCForge is designed to explore the following security engineering workflows:

- Generate synthetic security events representing normal and suspicious activity.
- Model organizational environments containing users, departments, and endpoints.
- Simulate attack scenarios for defensive security training.
- Associate simulated attack events with relevant MITRE ATT&CK technique identifiers.
- Export telemetry into structured formats for downstream analysis.
- Test detection logic against labelled simulation data.
- Experiment with API-driven simulation and real-time event delivery.

## Key Features

### 1. Enterprise Environment Simulation

The planned environment-generation capabilities model organizations with departments, users, workstations, and servers. This provides context for generating security telemetry across a simulated organization.

### 2. Baseline Activity Generation

SOCForge is designed to model routine office activity, such as authentication events, file access, DNS requests, and network activity. Baseline events can provide comparison data for investigating suspicious behavior.

### 3. Attack Scenario Simulation

The project includes a framework for representing security scenarios through synthetic event sequences. These scenarios are intended to support defensive analysis and detection development.

### 4. MITRE ATT&CK Mapping

Simulated attack activity can be associated with relevant MITRE ATT&CK technique identifiers. This helps organize test cases and relate generated events to recognized adversary behaviors.

Example technique identifiers include:

- **T1110** — Brute Force
- **T1566** — Phishing
- **T1486** — Data Encrypted for Impact

Technique mappings should be interpreted as simulation labels, not evidence that a real attack occurred.

### 5. Detection Rule Evaluation

SOCForge is intended to support testing detection logic against labelled event data. A useful evaluation workflow compares expected attack labels with detection results and documents the assumptions behind each metric.

Support for particular rule formats, detection engines, and automated scoring depends on the implemented modules and their tests.

### 6. Telemetry Export

The project targets common formats for security event processing:

- JSON
- Newline-delimited JSON (NDJSON)
- CSV
- RFC 5424 Syslog
- Elastic Common Schema (ECS)

Check the current implementation and examples before relying on a specific exporter in an operational workflow.

### 7. API and Event Streaming

SOCForge is designed around a Python API, with planned or implemented endpoints for environment creation, simulation generation, detection-rule access, and simulation control. WebSocket streaming is intended to support real-time event consumption where available.

## Attack Scenarios

The scenario catalogue includes the following planned or documented use cases:

| Scenario | Defensive investigation focus |
|---|---|
| Brute-force activity | Repeated authentication failures and potential account lockouts |
| Phishing | Simulated email delivery, suspicious links, and credential-harvesting indicators |
| Persistence | Simulated registry, scheduled-task, or service-related events |
| Privilege escalation | Suspicious privilege changes and administrative group modifications |
| Lateral movement | Remote logons and movement between simulated hosts |
| Insider threat | Unusual access to sensitive files and simulated data movement |
| Ransomware | Suspicious file activity and simulated impact-related indicators |
| Multi-stage attack | Correlated events across several stages of a simulated intrusion |

Scenario availability and event detail depend on the current implementation. These scenarios generate synthetic telemetry and should not be treated as instructions for executing attacks against real systems.

## Technology and Architecture

SOCForge is organized as a Python application with modules intended to separate API handling, simulation logic, telemetry generation, alerting, detection, and data storage.

The repository structure includes:

- **API:** Application endpoints and request handling.
- **Core:** Environment configuration, business activity, and simulation logic.
- **Telemetry:** Event generation and output formatting.
- **Alert:** Alert-related logic.
- **Detection:** Detection rules, validation, and scoring.
- **Database:** Data models and database connectivity.
- **Tests:** Automated verification of application behavior.

The application entry point is `src.app.main`, according to the documented setup below.

## Project Structure

```text
SOCForge/
├── docker/
│   ├── backend.Dockerfile
│   └── docker-compose.yml
├── src/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── telemetry/
│   │   ├── alert/
│   │   ├── detection/
│   │   ├── db/
│   │   ├── config.py
│   │   └── main.py
│   └── tests/
├── requirements.txt
└── README.md
```

The structure above describes the intended organization; consult the repository for the exact files currently present.

## Getting Started

### Prerequisites

- Python 3.10 or later
- Git
- `pip`

### 1. Clone the repository

```bash
git clone https://github.com/Syedraihaan/SOCForge.git
cd SOCForge
```

### 2. Create a virtual environment

**Windows (PowerShell):**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

**Linux/macOS:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Start the application

```bash
uvicorn src.app.main:app --reload
```

If the application starts successfully, open:

- **API documentation:** http://127.0.0.1:8000/docs
- **Alternative API documentation:** http://127.0.0.1:8000/redoc

If startup fails, verify the Python version, installed dependencies, required environment variables, and database configuration.

## Running with Docker

The repository includes Docker-related configuration. From the project root, try:

```bash
docker compose -f docker/docker-compose.yml up --build
```

Confirm that the Compose configuration, environment variables, service dependencies, and exposed ports match your local setup. The command should be considered a verified setup method only after testing it against the current repository.

To stop the services:

```bash
docker compose -f docker/docker-compose.yml down
```

## API Documentation

When the application is running, use `/docs` to inspect the actual registered endpoints, required request fields, and response schemas.

The following routes describe the intended API surface; confirm their availability in the running application before using them.

| Endpoint | Method | Intended purpose |
|---|---|---|
| `/api/v1/companies` | POST | Create a simulated organization |
| `/api/v1/companies/{company_id}/topology` | GET | Retrieve organization topology |
| `/api/v1/simulations/generate` | POST | Generate a simulation dataset |
| `/api/v1/detections/rules` | GET | Retrieve available detection rules |
| `/api/v1/stream/{simulation_run_id}` | WebSocket | Consume simulation events |
| `/api/v1/simulations/{simulation_run_id}/control` | POST | Control a running simulation |

Request parameters, response formats, supported actions, and error behavior are determined by the implemented API schemas.

## Testing

Run the test suite from the project root:

```bash
python -m pytest
```

For a more detailed test report:

```bash
python -m pytest -v
```

To measure test coverage, if `pytest-cov` is installed:

```bash
python -m pytest --cov=src/app --cov-report=term-missing
```

Report test results only after running the tests in your environment. Passing unit tests alone do not prove that all integrations, exporters, or simulation scenarios work end to end.

## Example Workflow

A typical SOCForge evaluation workflow is:

1. Start the application and inspect the API documentation.
2. Create a simulated organization, if supported.
3. Generate a supported attack scenario.
4. Inspect the resulting events and their simulation labels.
5. Export the telemetry using an implemented output format.
6. Run a detection rule against the generated data.
7. Compare detection results with the expected simulation labels.
8. Document the results, limitations, and steps needed to reproduce the experiment.

For a strong reproducible demonstration, include a real request, representative output, and test results from your own execution of this workflow.

## Security and Limitations

- SOCForge is intended for defensive security education, research, and controlled simulation.
- Generated events are synthetic and may not accurately reproduce every detail of a real enterprise environment.
- ATT&CK mappings represent intended scenario labels and do not independently establish malicious activity.
- Detection results depend on event quality, rule logic, test data, and evaluation methodology.
- SIEM compatibility should be verified with the specific version, schema, and configuration being used.
- Do not connect experimental simulation components to production systems without reviewing their behavior and security implications.
- Keep credentials, API keys, private data, and environment-specific secrets out of source control.

## Roadmap

Potential areas for future development include:

- [ ] Reproducible end-to-end demonstrations for each supported scenario.
- [ ] Expanded unit and integration test coverage.
- [ ] Validated telemetry exporters and sample datasets.
- [ ] Documented detection evaluation metrics.
- [ ] Improved event correlation and timeline visualization.
- [ ] Verified SIEM ingestion examples.
- [ ] CI workflows for automated testing and quality checks.
- [ ] Architecture diagrams and sample investigation reports.

This roadmap is not a claim that these capabilities are already implemented.

## Contributing

Contributions, bug reports, documentation improvements, and reproducible test cases are welcome.

When submitting a contribution:

1. Describe the problem or improvement.
2. Include steps to reproduce any reported issue.
3. Add or update tests where appropriate.
4. Document configuration requirements and limitations.
5. Avoid including credentials, private logs, or sensitive organizational data.

Open an issue or submit a pull request through the GitHub repository.

## License

Please refer to the repository's `LICENSE` file for the applicable license and permitted use. If no license file is present, check the licensing status before redistributing or reusing the code.

---

**SOCForge — Simulate security events. Evaluate detections. Learn through investigation.**

## REST API Reference

| Endpoint | Method | Params | Description |
| :--- | :--- | :--- | :--- |
| `/api/v1/companies` | `POST` | `name`, `industry`, `size` | Create corporate topology |
| `/api/v1/companies/{company_id}/topology` | `GET` | - | Get generated host/user information |
| `/api/v1/simulations/generate` | `POST` | `company_id`, `scenario`, `duration_hours`, `format` | Export simulated datasets |
| `/api/v1/detections/rules` | `GET` | `technique_id` | Get detection rules (Sigma, Wazuh, Splunk) |
| `/api/v1/stream/{simulation_run_id}` | `WS` | - | WebSocket real-time telemetry stream |
| `/api/v1/simulations/{simulation_run_id}/control` | `POST` | `action`, `value` | Control running stream (pause/resume/speed) |

---

## Verification & Testing
Execute the test suite using pytest:
```bash
python -m pytest
```
