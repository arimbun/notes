# Istio in Action

Book: https://www.manning.com/books/istio-in-action

## 1. Intro to Istio Service Mesh

- Service mesh?
  - A distributed application infrastructure
  - Handles network traffic on behalf of application
  - Application does not know about the mesh (i.e. it is *transparent*)
- Service mesh data plane
  - Traffic goes through here
  - Data plane establishes, secures and controls traffic
- Envoy proxy as a sidecar:
  - All network traffic to/from an application goes through the proxy
- Envoy proxy supports:
  - Retries
  - Timeouts
  - Circuit-breaking
  - Client-side load balancing (adaptive and zone-aware)
  - Service discovery
  - Security
  - Metrics collection w/o framework or language dependencies e.g.
    - requests/second
    - no. of failures
    - no. of circuit-breaking events
  - Collect distributed tracing spans (i.e. trace each step of a request)
- Istio
  - Open source version of Google, IBM and Lyft's mesh
  - Istio's data plane helps deploy Envoy as a sidecar to applications
  - Implements control points at each application to enable networking
    - This enables canary releases, dark launches, gradual rollouts
  - **Security**
    - Traffic encrypted by default
    - Key management
    - Certificate issuance (+ workload identity embedded)
    - Certificate installation
    - Certificate rotation
    - mTLS out-of-the-box
- Mesh vs. ESB (Enterprise Service Bus)
  - ESB requires service integration gatekeepers => creates silos
  - ESB is very centralised deployment
  - ESB mixes app networking and service mediation concerns
  - ESB implementation is (often) complicated and proprietary
- Mesh vs. API Gateway
  - API Gateway provides public endpoints for an org's public APIs
  - API Gateway provides:
    - security
    - rate limiting
    - quota management
    - metrics collection
    - specs
    - user registration
    - billing, etc.
  - API Gateway *may* be used to centralise (for internal APIs):
    - security
    - policy
    - metrics collection
  - API Gateway drawbacks:
    - Centralised deployment, hence:
      - Application cannot secure transport layer
      - To secure transport, app needs to participate in security config
    - Two hops needed for inter-app comms:
      - one hop to the API gateway
      - one hop to the target app
    - (In general) No circuitbreaker or bulkheading 
- Service mesh drawbacks
  - Additional middleware (i.e. Envoy proxy)
  - Knowledge of Envoy proxy required (for debugging)
  - Proper tenancy and isolation models required
    - else Mesh becomes single point of failure

## 2. First Steps with Istio

- **Istio Control Plane** provides:
  - APIs for operators to configure Routing/Resilience behaviour
  - APIs for the Data Plane to consume their configuration
  - APIs for specifying Usage Policies
  - Service Discovery abstraction for the Data Plane
  - Certificate issuance and rotation
  - Workload identity assignment
  - Unified telemetry collection
  - Service-proxy Sidecar injection
  - Network boundaries specs
- **Istio Pilot (istiod)**
  - Converts Istio config => Sidecar proxy config
- **Istio uses K8s CRDs**
- Istio's data plane API *implements* Envoy discovery API
-   

## 12. Scaling Istio

- Istio multi-cluster deployment models
  - primary-remote (single/shared control plane)
  - primary-primary (replicated control plane)
  - external control plane
- Cross-Cluster Discovery
  - Service Accounts
    - identity to non human clients
  - Service Account Tokens
    - auto-generated for each service account
    - represent identity claim
    - formatted as JWT
    - injected by K8s into Pods that use tokens to auth with API server
  - Roles & Cluster Roles
    - a set of permissions for identity
- Common trust between clusters
  - Clusters in a flat network?
    - Workloads simply connect via IP
  - Clusters in different networks?
    - Use special **Istio ingress gateways** located at edge of the network and proxy cross-cluster traffic
- Istiod
  - Validates and approves CSRs stored in K8s
  - CSRs are submitted to external CA through one of the following:
    - cert-manager
    - custom K8s controller
  - Cert signed by external CA is stored in K8s CSR
  - Istiod forwards cert to workload using Secret Discovery Service (SDS)
