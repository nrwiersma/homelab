<picture>
  <source media="(prefers-color-scheme: dark)" srcset="http://svg.wiersma.co.za/github/project.v2?title=homelab&tag=k8s%20homelab&mode=dark">
  <source media="(prefers-color-scheme: light)" srcset="http://svg.wiersma.co.za/github/project.v2?title=homelab&tag=k8s%20homelab">
  <img alt="Logo" src="http://svg.wiersma.co.za/github/project.v2?title=homelab&tag=k8s%20homelab">
</picture>

[![Build Status](https://github.com/nrwiersma/homelab/actions/workflows/tests.yml/badge.svg)](https://github.com/nrwiersma/infra/actions)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/hamba/avro/master/LICENSE)

## Bootstrapping

#### Install ArgoCD

To bootstrap the cluster, Argo CD needs to be installed manually. Run the commands below:

```shell
kubectl create namespace argocd
kubectl apply -n argocd -k infra/argocd/overlays/homelab
```

#### Create 1Password Secrets

In order for `external-secrets` to sync the secrets into the cluster, it needs access to
1Password. Create the following secrets in the cluster:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: onepassword-connect-token
  namespace: external-secrets
type: Opaque
stringData:
  token: my-token
---
apiVersion: v1
kind: Secret
metadata:
  name: onepassword-connect-credentials
  namespace: external-secrets
type: Opaque
stringData:
  # NOTE: This secret value must be base64 encoded after it becomes the OP_SESSION env var in the Connect Server Deployment, that means double base64 encoded here. (Or single w/ stringData.)
  1password-credentials.json: |-
    base64-encoded-file
```

#### Setup Applications

To finish bootstrapping the cluster, apply the ArgoCD applications and projects:

```shell
kubectl apply --recursive -f cluster/
```
