# AWS Distro for Open Telemetry (ADOT) EKS Add-On

**Important Pre-requisite - Needed for Amazon Managed Grafana**
- Enable AWS IAM Identity Center

# OPEN TELEMETRY Terraform Project folder structure
```bash
├── 01_EKS_Cluster_Environment
│   ├── 05_OPENTELEMTRY_terraform-manifests
│   │   ├── c1_versions.tf
│   │   ├── c2_variables.tf
│   │   ├── c3_01_vpc_remote_state.tf
│   │   ├── c3_02_eks_remote_state.tf
│   │   ├── c4_datasources_and_locals.tf
│   │   ├── c5_helm_and_kubernetes_providers.tf
│   │   ├── c6_01_adot_collector_iam_role.tf
│   │   ├── c6_02_adot_collector_iam_policy.tf
│   │   ├── c6_03_adot_pod_identity_association.tf
│   │   ├── c6_04_eks_addon_certmanager.tf
│   │   ├── c6_05_eks_addon_adot.tf
│   │   ├── c6_06_eks_addon_prometheus_node_exporter.tf
│   │   ├── c6_07_eks_addon_kube_state_metrics.tf
│   │   ├── c6_09_adot_k8s_cluster_role_and_rolebinding.tf
│   │   ├── c7_amp_prometheus_workspace.tf
│   │   ├── c8_01_amg_grafana_iam_policy.tf
│   │   ├── c8_02_amg_grafana_iam_role.tf
│   │   ├── c8_03_amg_grafana.tf
│   │   └── terraform.tfvars
```

# End-to-End Tracing on EKS using AWS ADOT (OpenTelemetry)
---
**Introduction**
---
- Implement OpenTelemetry ADOT Traces for Retail Store Application
- Traces are sent to AWS X-Ray via ADOT Collector
- View traces in AWS CloudWatch Application Signals conolse
- Includes health check filtering to reduce costs and noise

**Trace flow**
```bash
Retail Apps → ADOT Collector → AWS X-Ray → CloudWatch Application Signals
```

# ADOT Collector Traces Architecture
![alt](https://github.com/nawaz-ch/ADOT-AMP-AMG/blob/6f773c4c7f784b50f802f2a514426a77d79af5b4/20_02_01_ADOT_OpenTelemetry_Traces.png)


# ADOT Collector YAML Structure
![alt](https://github.com/nawaz-ch/ADOT-AMP-AMG/blob/6f773c4c7f784b50f802f2a514426a77d79af5b4/20_04_02_OpenTelemetry_Metrics_AMP_AMG.png)


> ⚠️ Note: Make sure EKS cluster and retail application store are up and running .Also run OPENTELEMETRY_terraform-mainfests.

# Review 01_adot_collector_traces.yaml
- This defines how traces are received, processed, filtered, and exported to AWS X-Ray using ADOT.

**Important:**
- We are NOT using auto-instrumentation agents (Java agent, Node agent, etc.)
- We are ONLY injecting OTEL SDK runtime configuration
- Application code already contains manual instrumentation

# Deploy Traces ADOT Collector and Instrumentation
```bash
# Change Directory 
cd OpenTelemetry_Traces

# Deploy ADOT Collector and Review Logs
kubectl apply -f 01_OpenTelemetry_Traces/01_adot_collector_traces.yaml

# Verify ADOT Collector and Deployment
kubectl get opentelemetrycollector
kubectl get deploy
kubectl describe deployment adot-traces

# Verify if this collector is part of ADOT Operator installed via EKS Addon
kubectl describe deployment adot-traces | grep operator

# Review ADOT Collector Logs
kubectl get pods
kubectl logs -f <POD-NAME>
or 
kubectl logs -f -l  app.kubernetes.io/name=adot-traces-collector

# Deploy ADOT Instrumentation 
kubectl apply -f 01_OpenTelemetry_Traces/02_adot_instrumentation_traces.yaml

# Verify ADOT Instrumentation
kubectl get instrumentation
```

**Restart all your microservices.**

**Verify Instrumentation Injection**
```bash
# Verify ADOT Collector Logs
kubectl logs -f -l app.kubernetes.io/name=adot-traces-collector

# Verify Retail App Logs
kubectl logs -f -l app.kubernetes.io/name=catalog
kubectl logs -f -l app.kubernetes.io/name=carts
kubectl logs -f -l app.kubernetes.io/name=checkout
kubectl logs -f -l app.kubernetes.io/name=orders
kubectl logs -f -l app.kubernetes.io/name=ui

```

# Review the CloudWatch Traces
- Go to AWS Cloud Watch* -> Application Signals -> Traces What you'll see:
- Complete end-to-end request flow (UI → Carts → Checkout → Orders)
- Database queries with sanitized SQL
- HTTP calls with status codes and latencies
- Kubernetes metadata (pod name, namespace, deployment, node)

# Clean-Up
```bash
# Delete Instrumentation
kubectl delete -f 01_OpenTelemetry_Traces/02_adot_instrumentation_traces.yaml

# Delete Traces Collector
kubectl delete -f 01_OpenTelemetry_Traces/01_adot_collector_traces.yaml
```



