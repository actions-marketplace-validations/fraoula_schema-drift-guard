# Fraoula Schema Drift Guard

[![GitHub Release](https://img.shields.io/github/v/release/fraoula/schema-drift-guard?color=purple)](https://github.com/fraoula/schema-drift-guard/releases)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Marketplace](https://img.shields.io/badge/Marketplace-Fraoula%20Schema%20Drift%20Guard-blueviolet)](https://github.com/marketplace/actions/fraoula-schema-drift-guard)

Detect breaking schema changes and sensitive data creep in CI/CD before bad data reaches production.

Built by [Fraoula](https://www.fraoula.co) — data trust software for regulated enterprises.

---

## Overview

Modern data platforms break when upstream producers change data contracts without warning. **Fraoula Schema Drift Guard** is a lightweight, zero-dependency GitHub Action that automates schema verification on every pull request.

- **Zero dependencies**: Uses Python 3 standard library; executes in milliseconds.
- **Breaking change detection**: Automatically fails CI when required fields are deleted or types change.
- **Sensitive data audit**: Flags newly introduced PII and sensitive fields (SSN, credit card, IBAN, PAN, etc.) with automated warnings and risk scores.
- **Runs anywhere**: Compatible with `ubuntu-latest`, `windows-latest`, and `macos-latest`.
- **100% Local execution**: No data or schemas ever leave your GitHub runner.

---

## Quick Start

Add the following step to your GitHub Actions workflow:

```yaml
- name: Run Schema Drift Guard
  uses: fraoula/schema-drift-guard@v0.1.0
  with:
    baseline: schemas/baseline.json
    incoming: schemas/incoming.json
```

---

## Complete Workflow Example

Create `.github/workflows/schema-drift.yml` in your repository:

```yaml
name: Schema Drift Verification

on:
  pull_request:
    paths:
      - 'schemas/**'
  push:
    branches:
      - main

jobs:
  verify-schema:
    name: Schema Drift Check
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Fraoula Schema Drift Guard
        uses: fraoula/schema-drift-guard@v0.1.0
        with:
          baseline: examples/baseline.json
          incoming: examples/incoming.json
```

---

## Action Inputs

| Input | Required | Description |
|---|---|---|
| `baseline` | **Yes** | Relative path to the approved baseline schema JSON file in your repository. |
| `incoming` | **Yes** | Relative path to the proposed/incoming schema JSON file in your repository. |

---

## Schema File Format

Schema files are simple JSON objects mapping field names to their expected types:

```json
{
  "customer_id": "integer",
  "name": "string",
  "email": "string",
  "account_status": "string"
}
```

---

## Classification & Exit Codes

| Severity | Description | Exit Code | Action Outcome |
|---|---|---|---|
| **PASS** | No changes or only safe additions detected. | `0` | CI passes |
| **WARN** | New sensitive / PII fields detected. | `0` | CI passes with warning summary |
| **FAIL** | Required field removed or field type changed. | `1` | CI fails (PR blocked) |
| **ERROR** | Malformed JSON or invalid arguments. | `2` | CI fails (execution error) |

### Monitored Sensitive Fields (PII)
Automatically audited tokens include: `ssn`, `social_security_number`, `email`, `email_address`, `phone`, `phone_number`, `mobile`, `dob`, `date_of_birth`, `passport`, `credit_card`, `card_number`, `cvv`, `bank_account`, `iban`, `aadhaar`, `aadhar`, `pan`, `tax_id`, `medical_record_number`, `mrn`.

---

## Sample Report Output

When a breaking change or sensitive field is introduced:

```text
Fraoula Schema Drift Report
==============================

Status: FAIL
Risk Score: 70/100

BREAKING
✗ Required field removed: account_status
✗ customer_id: integer → string

WARNING
⚠ New sensitive field detected: phone

SAFE
✓ Optional field added: preferred_language

Powered by Fraoula
https://www.fraoula.co
```

---

## Local Usage

You can run Drift Guard locally without GitHub Actions:

```bash
python drift_guard.py examples/baseline.json examples/incoming.json
```

Run test suite:

```bash
python -m unittest discover -s tests
```

---

## Security & Privacy

Fraoula Schema Drift Guard operates **100% locally** inside your CI runner. It does not transmit schema contents, telemetry, or metadata to any external service or server.

---

## License

This project is licensed under the [Apache License 2.0](LICENSE).

---

## About Fraoula

[Fraoula](https://www.fraoula.co) provides data trust, verification, and governance infrastructure for regulated enterprises. Explore our open-source tools and enterprise solutions at [fraoula.co](https://www.fraoula.co).
