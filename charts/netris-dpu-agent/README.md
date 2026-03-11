# Netris DPU Agent

Netris DPU Agent runs as a DaemonSet on the DPU cluster (tenant cluster), deployed alongside the HBN DPU Service in the `dpf-operator-system` namespace. It connects each NVIDIA BlueField-3 DPU to the Netris controller.

## Prerequisites

- [DPF Operator](https://github.com/nvidia/doca-platform) v25.10.0+ deployed on the host cluster
- HBN DPU Service running in the `dpf-operator-system` namespace on the DPU (tenant) cluster
- Helm 3+

> The `hbn.url` parameter must point to the in-cluster Kubernetes Service name of the HBN instance (e.g. `https://dpu-cplane-tenant1-doca-hbn:8765`). HBN must be deployed and healthy before installing this chart.

## Installing the Chart

Add the Netris Helm repository:

```
helm repo add netrisai https://netrisai.github.io/charts
```

Update your local Helm chart repository cache:

```
helm repo update
```

Install the chart into the `dpf-operator-system` namespace alongside HBN:

```
helm install netris-dpu-agent netrisai/netris-dpu-agent \
  --namespace dpf-operator-system \
  --set controller.host="netris.example.com" \
  --set controller.authKey="your-auth-key" \
  --set hbn.url="https://dpu-cplane-tenant1-doca-hbn:8765" \
  --set hbn.password="your-hbn-password"
```

> **Note:** `controller.host`, `controller.authKey`, `hbn.url`, and `hbn.password` are required. The chart will deploy but the agent will fail to connect without them.

## Uninstalling the Chart

To uninstall/delete the `netris-dpu-agent` helm release:

```
helm uninstall netris-dpu-agent
```

## Configuration

The following table lists the configurable parameters of the netris-dpu-agent chart and their default values.

### Controller parameters

| Parameter                  | Description                                              | Default               |
| -------------------------- | -------------------------------------------------------- | --------------------- |
| `controller.host`          | Netris controller hostname or IP (**required**)          | `netris.example.com`  |
| `controller.authKey`       | Authentication key for the Netris controller (**required**) | `authkey`          |
| `controller.grpcPort`      | gRPC port of the Netris controller                       | `50051`               |
| `controller.telescopePort` | Telescope port of the Netris controller                  | `3033`                |
| `controller.timeout`       | Connection timeout in seconds                            | `30`                  |

### HBN (Host-Based Networking) parameters

| Parameter            | Description                                              | Default                                        |
| -------------------- | -------------------------------------------------------- | ---------------------------------------------- |
| `hbn.url`            | In-cluster Kubernetes Service URL of the HBN DPU Service (**required**) | `https://dpu-cplane-tenant1-doca-hbn:8765` |
| `hbn.username`       | HBN API username                                         | `hbn`                                          |
| `hbn.password`       | HBN API password (**required**)                          | `password`                                     |
| `hbn.skipTlsVerify`  | Skip TLS verification for HBN API                        | `true`                                         |
| `hbn.timeout`        | HBN API connection timeout in seconds                    | `30`                                           |

### Common parameters

| Parameter                   | Description                                                                                               | Default                          |
| --------------------------- | --------------------------------------------------------------------------------------------------------- | -------------------------------- |
| `nameOverride`              | String to partially override the fullname template (will prepend the release name)                        | `""`                             |
| `fullnameOverride`          | String to fully override the fullname template                                                            | `""`                             |
| `imagePullSecrets`          | Reference to one or more secrets to be used when pulling images                                           | `[]`                             |
| `serviceAccount.create`     | Create a ServiceAccount for the DaemonSet                                                                 | `false`                          |
| `serviceAccount.name`       | Use the ServiceAccount with the specified name                                                            | `""`                             |
| `serviceAccount.annotations`| Annotations to add to the ServiceAccount                                                                  | `{}`                             |
| `podAnnotations`            | Pod annotations                                                                                           | `{}`                             |
| `podSecurityContext`        | Pod Security Context                                                                                      | `{}`                             |
| `securityContext`           | Container Security Context                                                                                | `{}`                             |
| `updateStrategy.type`       | DaemonSet update strategy type                                                                            | `RollingUpdate`                  |
| `updateStrategy.rollingUpdate.maxUnavailable` | Maximum number of unavailable pods during rolling update                            | `1`                              |
| `resources.limits.cpu`      | CPU limit                                                                                                 | `1`                              |
| `resources.limits.memory`   | Memory limit                                                                                              | `1Gi`                            |
| `resources.requests.cpu`    | CPU request                                                                                               | `1`                              |
| `resources.requests.memory` | Memory request                                                                                            | `1Gi`                            |
| `nodeSelector`              | Node labels for pod assignment                                                                            | `{}`                             |
| `tolerations`               | Node tolerations for pod assignment                                                                       | `[]`                             |
| `affinity`                  | Node affinity for pod assignment                                                                          | `{}`                             |

### Image parameters

| Parameter            | Description                                                              | Default                        |
| -------------------- | ------------------------------------------------------------------------ | ------------------------------ |
| `image.repository`   | Image repository                                                         | `netrisai/bare-metal-dpu-agent`|
| `image.tag`          | Image tag. Overrides the tag whose default is the chart `appVersion`     | `4.7.0.003`                    |
| `image.pullPolicy`   | Image pull policy                                                        | `IfNotPresent`                 |
