## 📝 **Prometheus Temporary Metrics Forwarding Setup (OpenShift)**

### 🎯 **Goal**
We are deploying a **temporary standalone Prometheus instance** inside OpenShift. Its sole purpose is to:

1. **Scrape metrics** from a specific Kubernetes service endpoint.
2. **Push (remote write)** those metrics to an external Prometheus (or Prometheus-compatible) remote storage backend.

This setup is **lightweight**, **ephemeral**, and avoids integrating with full Prometheus Operator stack or persistent storage.

---

### 🏗️ **What This Setup Includes**

- A **ConfigMap** defining Prometheus scraping and remote write configuration.
- A **Secret** storing a Bearer Token used for authenticating with the metrics endpoint.
- A **Deployment** that:
  - Runs the official `quay.io/prometheus/prometheus:main` image.
  - Mounts the config and secret.
  - Uses an ephemeral `emptyDir` volume for required TSDB files.
  - Is configured with restricted `securityContext` for OpenShift compatibility.
- (Optional) A **Service** and **Route** to expose Prometheus UI (not included here).

---

### 🔗 **Scraping Target**
Prometheus is configured to scrape metrics from this internal K8s service:

```
http://controller-edb-01-service.aap-24/api/v2/metrics/
```

- Uses a **Bearer Token** via `bearer_token_file` for authentication.
- Scrape interval: `15s`.

---

### 🚀 **Remote Write Target**
Scraped metrics are pushed to an external Prometheus-compatible endpoint using Prometheus's `remote_write` feature.

> You can plug in any remote backend: Prometheus, Cortex, Thanos, Mimir, etc.

---

### 🧪 **Why This Approach**
- **Temporary and flexible** — no persistent volumes, no extra CRDs or operators.
- **Ideal for testing**, temporary forwarding, or when full Prometheus stack isn’t available.
- Keeps config simple and self-contained in one namespace.
- Fully compliant with OpenShift’s default `restricted` SCC.

---

