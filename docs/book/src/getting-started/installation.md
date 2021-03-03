# Installation

## Install the Secrets Store CSI Driver and provider

### Prerequisites

#### Supported kubernetes versions

Recommended Kubernetes version:

- **v1.16.0+** (For Linux)
- **v1.18.0+** (For Windows)

### Deployment using Helm

Secrets Store CSI Driver allows users to customize their installation via Helm.

> Recommended to use Helm3

```bash
helm repo add secrets-store-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/secrets-store-csi-driver/master/charts
helm install csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver
```

Running the above `helm install` command will install the Secrets Store CSI Driver on Linux nodes.

#### Values

For a list of customizable values that can be injected when invoking helm install, please see the [Helm chart configurations](https://github.com/kubernetes-sigs/secrets-store-csi-driver/tree/master/charts/secrets-store-csi-driver#configuration).

### [Alternatively] Deployment using yamls

```bash
kubectl apply -f deploy/rbac-secretproviderclass.yaml
kubectl apply -f deploy/csidriver.yaml
kubectl apply -f deploy/secrets-store.csi.x-k8s.io_secretproviderclasses.yaml
kubectl apply -f deploy/secrets-store.csi.x-k8s.io_secretproviderclasspodstatuses.yaml
kubectl apply -f deploy/secrets-store-csi-driver.yaml

# If using the driver to sync secrets-store content as Kubernetes Secrets, deploy the additional RBAC permissions
# required to enable this feature
kubectl apply -f deploy/rbac-secretprovidersyncing.yaml

# [OPTIONAL] For kubernetes version < 1.16 running `kubectl apply -f deploy/csidriver.yaml` will fail. To install the driver run
kubectl apply -f deploy/csidriver-1.15.yaml

# [OPTIONAL] To deploy driver on windows nodes
kubectl apply -f deploy/secrets-store-csi-driver-windows.yaml
```

To validate the installer is running as expected, run the following commands:

```bash
kubectl get po --namespace=kube-system
```

You should see the Secrets Store CSI driver pods running on each agent node:

```bash
csi-secrets-store-qp9r8         3/3     Running   0          4m
csi-secrets-store-zrjt2         3/3     Running   0          4m
```

You should see the following CRDs deployed:

```bash
kubectl get crd
NAME                                               
secretproviderclasses.secrets-store.csi.x-k8s.io
secretproviderclasspodstatuses.secrets-store.csi.x-k8s.io
```

<aside class="note warning">
<h1>Warning</h1>

**v0.0.17** and earlier installed the driver to the `default` namespace.
Newer versions of the driver will install the driver to the `kube-system`
namespace. After applying the new YAML files to your cluster run the following
to clean up old resources:

```bash
kubectl delete daemonset csi-secrets-store --namespace=default
kubectl delete daemonset csi-secrets-store-windows --namespace=default
kubectl delete serviceaccount secrets-store-csi-driver --namespace=default
```

</aside>
</details>

## Use the Secrets Store CSI Driver with a Provider

Now that the Secrets Store CSI Driver has been deployed, select a provider from the supported provider list, then follow the installation steps for the provider:

- [Azure Provider](https://azure.github.io/secrets-store-csi-driver-provider-azure/)
- [Vault Provider](https://github.com/hashicorp/secrets-store-csi-driver-provider-vault)
- [GCP Provider](https://github.com/GoogleCloudPlatform/secrets-store-csi-driver-provider-gcp)