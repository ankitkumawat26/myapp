# myapp
# DevOps CI/CD + GitOps Pipeline

End-to-end pipeline: a code push builds a Docker image, updates the manifests
repo, and ArgoCD deploys it to Kubernetes automatically.

## Architecture

```
Developer -> GitHub (myapp) -> Jenkins -> Docker Hub
                                   |
                                   v
                    GitHub (myapp-manifests)
                                   |
                                   v
                 ArgoCD -> MicroK8s (Ubuntu VM)
```

## Tools
Linux (Ubuntu), Docker, Git/GitHub, Jenkins, MicroK8s (Kubernetes), ArgoCD

## Flow
1. Push code to `myapp`. Jenkins detects it by polling SCM.
2. Jenkins builds the image, tags it `v<BUILD_NUMBER>` and pushes it to Docker Hub.
3. Jenkins commits the new tag to `myapp-manifests`.
4. ArgoCD sees the change and syncs the Deployment (rolling update).

## Challenges and fixes
- pip timeout inside Docker build on NAT network: re-ran the build, and added
  retry/timeout flags to the Dockerfile as a fallback.
- Jenkins could not use Docker: added the `jenkins` user to the `docker` group.
- Port clash with a local Jenkins on 8080: used host port 9090 in the VM port forwarding.
