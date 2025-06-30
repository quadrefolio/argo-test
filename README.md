# Argo CD Demo Application

## Overview
This repository demonstrates a GitOps deployment workflow with self-healing capabilities using Argo CD and Kubernetes probes with a demo application. The application implements startup, liveness, and readiness probes for robust health monitoring. The application source code and CI/CD pipeline are hosted on GitLab, while this repository manages the Kubernetes deployment configuration through Argo CD for continuous deployment.

## Repository Structure
```
├── app/
│   ├── deployment.yaml    # Kubernetes deployment configuration
│   └── servicel.yaml      # Kubernetes service configuration
└── argocd-app.yaml        # Argo CD application manifest
```

## CI/CD Workflow
1. Application source code is hosted on GitLab: https://gitlab.com/abdelrahman2054610/Abdelrahman
2. GitLab CI/CD pipeline:
   - Tests the application
   - Builds the container image
   - Pushes the new image to container registry
3. Argo CD watches this repository for deployment configuration changes
4. When a new image is available, update the deployment configuration in this repository
5. Argo CD automatically syncs the changes to the Kubernetes cluster

## Application Components

### Demo Application
- Container Image: `quadrefolio/demo-app:latest`
- Exposed Port: 8080
- Replicas: 5
- Health Checks:
  - Startup Probe: `/start`
  - Liveness Probe: `/live`
  - Readiness Probe: `/read`

### Service Configuration
- Type: NodePort
- Port Mapping: 80 -> 8080 (container port)
- Selector: `app: demo-app`

## Argo CD Configuration

### Application Details
- Name: argo-test
- Namespace: argocd
- Source Repository: https://github.com/quadrefolio/kubernetes-self-healing-gitops
- Path: app
- Target Revision: main

### Sync Policy
- Automated Sync Enabled
- Self Healing: Enabled
- Prune: Enabled
- Auto Create Namespace: Enabled

## Prerequisites
1. Kubernetes cluster
2. Argo CD installed in the cluster
3. `kubectl` CLI tool
4. Access to the container registry (image pull secret required: `regcred`)

## Deployment
1. Ensure Argo CD is installed in your cluster
2. Apply the Argo CD application manifest:
   ```bash
   kubectl apply -f argocd-app.yaml
   ```
3. Argo CD will automatically sync and deploy the application

## Monitoring
- Monitor the application status through the Argo CD UI
- Check application health using the configured probes:
  ```bash
  kubectl get pods -l app=demo-app
  kubectl describe deployment demo-app-deployment
  ```

## Repository Links
- Application Source & CI/CD Pipeline: https://gitlab.com/abdelrahman2054610/Abdelrahman
- Deployment Configuration (this repo): https://github.com/quadrefolio/kubernetes-self-healing-gitops

## Notes
- The application uses image pull secrets (`regcred`) for accessing the container registry
- The service is exposed via NodePort for external access
- Automated sync is enabled for continuous deployment
- GitLab CI/CD pipeline handles testing, building, and publishing the application image
- This repository focuses on Kubernetes deployment configuration and Argo CD integration
