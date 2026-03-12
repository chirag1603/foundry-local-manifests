# Foundry Local Manifests for Flux GitOps

Flux GitOps manifests for deploying **Foundry Local** (AI model inference) on **AKS Arc** running on **Azure Local**.

These manifests are consumed by [Flux v2](https://fluxcd.io/) via `Microsoft.KubernetesConfiguration/fluxConfigurations` (Bicep).

## Structure

```
├── cert-manager/                  Kustomization: cert-manager
│   ├── namespace.yaml               Creates cert-manager namespace
│   ├── helmrepo-jetstack.yaml       HelmRepository → https://charts.jetstack.io
│   ├── helmrelease-cert-manager.yaml HelmRelease → cert-manager v1.19.2
│   ├── helmrelease-trust-manager.yaml HelmRelease → trust-manager v0.20.3
│   └── kustomization.yaml
│
├── inference-operator/            Kustomization: inference-operator
│   ├── namespace.yaml               Creates foundry-local-operator namespace
│   ├── helmrepo-foundry-oci.yaml    HelmRepository (OCI) → mcr.microsoft.com
│   ├── helmrelease-operator.yaml    HelmRelease → inference-operator 0.0.1-prp.3
│   └── kustomization.yaml
│
└── model/                         Kustomization: model
    ├── modeldeployment-qwen.yaml    ModelDeployment CR (qwen2.5-0.5b CPU)
    └── kustomization.yaml
```

## Chart Sources (all public, no auth needed)

| Component | Source | URL |
|-----------|--------|-----|
| cert-manager | Helm repo | `https://charts.jetstack.io` |
| trust-manager | Helm repo | `https://charts.jetstack.io` |
| inference-operator | OCI (MCR) | `oci://mcr.microsoft.com/foundrylocalonazurelocal/helmcharts/helm` |
| qwen2.5-0.5b model | AI Foundry catalog | Downloaded by Inference Operator at runtime |

## Usage

Deploy via Bicep (see `azure-local-poc/` in bicep-registry-modules repo):

```powershell
# Step 1: Infrastructure (Flux + cert-manager + inference-operator)
az deployment group create -g <RG> -f foundry-infra.bicep -p foundry-infra.bicepparam

# Step 2: Model (ModelDeployment CR)
az deployment group create -g <RG> -f foundry-model.bicep -p foundry-model.bicepparam
```
