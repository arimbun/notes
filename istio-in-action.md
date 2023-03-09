# Istio in Action

Book: https://www.manning.com/books/istio-in-action

## Istio Multi-Cluster Deployment Model

- primary-remote (single/shared control plane)
- primary-primary (replicated control plane)
- external control plane

## Cross-Cluster Discovery

- Service Accounts
  - identity to non human clients
- Service Account Tokens
  - auto-generated for each service account
  - represent identity claim
  - formatted as JWT
  - injected by K8s into Pods that use tokens to auth with API server
- Roles & Cluster Roles
  - a set of permissions for identity

## Common Trust between Clusters

- Clusters in a flat network?
  - Workloads simply connect via IP
- Clusters in different networks?
  - Use special **Istio ingress gateways** located at edge of the network and proxy cross-cluster traffic

## External Cert Authority Integration

- Istiod
  - Validates and approves CSRs stored in K8s
  - CSRs are submitted to external CA through one of the following:
    - cert-manager
    - custom K8s controller
      - Cert signed by external CA is stored in K8s CSR
      - Istiod forwards cert to workload using Secret Discovery Service (SDS)
