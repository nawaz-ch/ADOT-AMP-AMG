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
![alt]()



