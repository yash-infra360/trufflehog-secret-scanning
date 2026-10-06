# Secret Scanning Workflows (TruffleHog OSS with Shallow Cloning)

This directory contains the central secret scanning architecture for the **Shipsy** organization.

---

## ⚡ Why Shallow Cloning?
Instead of downloading the entire repository git history (`fetch-depth: 0`, which can be slow on large repos), this workflow uses **Shallow Cloning**:
* It downloads **only the commits added in the specific Pull Request (`fetch-depth: PR commits + 1`)**.
* Scans complete in **3 to 10 seconds**!

---

## 📂 Workflow Files

### 1. `secret-scan-reusable.yml` (The Core Engine)
* **Path**: `.github/workflows/secret-scan-reusable.yml`
* **Purpose**: Reusable GitHub Action that performs the shallow-cloned TruffleHog scan with active API verification.
* **Inputs**:
  * `only-verified` (default: `true`): Only fails the build on **active/live** verified credentials to avoid false alarms.

### 2. `secret-scan.yml` (The Trigger for `shipsy/devops`)
* **Path**: `.github/workflows/secret-scan.yml`
* **Purpose**: Runs automatically on every Pull Request and Push to `master`/`main`/`develop` in this `devops` repository.

---

## 🚀 How Other Repositories Use This (4-Line Setup)

In any other repository in your GitHub organization (e.g. `backend`, `frontend`, `auth-service`), simply create `.github/workflows/security.yml`:

```yaml
name: "Security Checks"

on:
  pull_request:
    branches: [ master, main, develop ]

jobs:
  secrets:
    uses: shipsy/devops/.github/workflows/secret-scan-reusable.yml@master
    with:
      only-verified: true
```
