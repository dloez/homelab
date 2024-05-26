# Homelab - Base
Base configuration and service for supporting the Homelab. Contains:

- K8s cluster (K3s) with the following services:
    - 1Password Kubernetes Operator for secrets management
    - MetalLb replacing K3s ServiceLb.


## K8s Cluster Setup

### Requirements
- [Helm](https://helm.sh/) - v3.15.1
- [Helmfile](https://github.com/helmfile/helmfile) - v1.0.0-rc.0
- A [1Password Connect](https://developer.1password.com/docs/connect/) configured and ready to go. You will need both the `1password-credentials.json` and the token given when the automation workflow was created.

### Steps
1. Install K3s and setup the cluster:
`curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--disable servicelb" INSTALL_K3S_VERSION="v1.29.4+k3s1" sh -s -`.
2. Copy `/etc/rancher/k3s/k3s.yaml` to `~/.kube/confg` or configure `export KUBECONFIG=/etc/rancher/k3s/k3s.yaml` in your shell.
2. Setup `helmfile` by running `helmfile init`.
3. Place the `1password-credentials.json` file inside the `k8s` directory.
4. Aplly 1Password helm charts by running `ONEPASSWORD_TOKEN="{TOKEN_HERE}" helmfile -f k8s/helmfile.1password.yml.gotmpl apply`.
5. Create Grafana agent secret by running `kubectl apply -f k8s/grafana-agent-credentials.yml`.
6. Run `helmfile apply -f k8s/helmfile.system.yml.gotmpl apply`, this installs:
    - Grafana Agent
    - MetalLb
7. Modify the file `k8s/metallb-pools.yml` with the required address pools and apply the configuration by running `kubectl apply -f k8s/metallb-pools.yml`.
