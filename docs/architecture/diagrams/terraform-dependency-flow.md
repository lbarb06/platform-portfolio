# Terraform Dependency And Apply Order

```mermaid
flowchart TD
  A[terraform/01-core-infra\nVPC ALB ASG RDS Secrets DNS] --> B[terraform/02-k8s-platform\nEKS ECR OIDC]
  B --> C[terraform/03-gitops-argocd\nArgo CD + App Bootstrap]
  A --> D[terraform/04-observability\nCloudWatch Dashboards + Alarms]
  A --> E[terraform/05-infra-cicd\nCodePipeline + CodeBuild]

  B -. reads remote state from .-> A
  C -. reads remote state from .-> B
  D -. reads remote state from .-> A
  E -. targets module .-> A
```

## Recommended First-Time Apply Order

1. `terraform/01-core-infra`
2. `terraform/02-k8s-platform`
3. `terraform/03-gitops-argocd`
4. `terraform/04-observability`
5. `terraform/05-infra-cicd`

## Recommended Destroy Order

1. `terraform/05-infra-cicd`
2. `terraform/04-observability`
3. `terraform/03-gitops-argocd`
4. `terraform/02-k8s-platform`
5. `terraform/01-core-infra`

## Notes

- Destroy in reverse dependency order to avoid remote-state and provider dependency failures.
- Confirm no workloads are still running on EKS before destroying `terraform/02-k8s-platform`.
- Keep state backends intact until all dependent modules are fully destroyed.
