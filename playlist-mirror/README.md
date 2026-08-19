# Helm Chart for Playlist Mirror

See: [application repository](https://github.com/itobey/playlist-mirror)

Usage:
```
helm pull oci://ghcr.io/itobey/charts/playlist-mirror --version 0.1.0
```

Single-user, single-replica app: it keeps its job state in a local SQLite file under
`DATA_DIR`, so `replicaCount` must stay at `1` and `persistence.enabled` should stay `true`
(or point `persistence.existingClaim` at a volume you manage yourself) — without it, the
uploaded OAuth client secret, the token, and every job are lost on restart.

Before first use, set `config.publicBaseUrl` to wherever the app is actually reached
(e.g. `https://playlist.example.com`). Google sends the OAuth sign-in back to
`<publicBaseUrl>/oauth/callback`, so sign-in fails behind an ingress until this matches.
On first visit you land on `/setup` to upload a Google OAuth "Desktop app" client secret
JSON file and sign in.

Telegram notifications are optional and off by default. This chart intentionally does not
accept a plaintext bot token/chat id in `values.yaml` — set `telegram.secretRef.name` to an
existing secret with keys `TELEGRAM_BOT_TOKEN` and `TELEGRAM_CHAT_ID`, or skip it and set
those up from the app's own Telegram page instead (they're stored in the database from
then on).

# Changelog

## 0.1.0

- Initial chart.

## Parameters

### Replica Configuration

| Name           | Description                                                                                                                    | Value |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------| ----- |
| `replicaCount` | Number of replicas to deploy. Must stay at 1: state lives in a local SQLite file, so two pods would corrupt each other's writes. | `1`   |

### Image Configuration

| Name                | Description                                             | Value                                     |
| ------------------- | -------------------------------------------------------- | ------------------------------------------ |
| `image.repository`  | The image repository to pull from                       | `ghcr.io/itobey/playlist-mirror` |
| `image.tag`         | The image tag to pull, defaults to the chart's appVersion | `""`                                       |
| `image.pullPolicy`  | The Kubernetes image pull policy                        | `IfNotPresent`                            |
| `imagePullSecrets`  | Secrets used to pull the image from a private registry  | `[]`                                       |

### Name Overrides

| Name               | Description                                           | Value |
| ------------------ | ----------------------------------------------------- | ----- |
| `nameOverride`     | Partially override the name of the deployed resources | `""`  |
| `fullnameOverride` | Fully override the name of the deployed resources     | `""`  |

### Service Account Configuration

| Name                         | Description                                                        | Value  |
| ---------------------------- | ------------------------------------------------------------------ | ------ |
| `serviceAccount.create`      | Specifies whether a service account should be created              | `true` |
| `serviceAccount.annotations` | Annotations to add to the service account                          | `{}`   |
| `serviceAccount.name`        | The name of the service account to use (ignored if create is true) | `""`   |

### Pod Annotations

| Name             | Description                   | Value |
| ---------------- | ----------------------------- | ----- |
| `podAnnotations` | Annotations to add to the pod | `{}`  |

### Pod Security Context

| Name                 | Description                      | Value |
| -------------------- | -------------------------------- | ----- |
| `podSecurityContext` | The security context for the pod | `{}`  |

### Security Context

| Name              | Description          | Value |
| ----------------- | -------------------- | ----- |
| `securityContext` | The security context | `{}`  |

### Service configuration

| Name           | Description                                                              | Value       |
| -------------- | ------------------------------------------------------------------------ | ----------- |
| `service.type` | The type of service to use. Can be ClusterIP, NodePort, or LoadBalancer. | `ClusterIP` |
| `service.port` | The port that the service will expose.                                   | `8000`      |

### Ingress configuration

| Name                 | Description                               | Value  |
| -------------------- | ----------------------------------------- | ------ |
| `ingress.enabled`    | Enable or disable the ingress.            | `true` |
| `ingress.className`  | The class name of the ingress controller. | `""`   |
| `ingress.annotations`| Annotations to add to the ingress.        | `{}`   |
| `ingress.hosts.host` | Host name of the ingress.                 |        |
| `ingress.tls`        | TLS configuration for the ingress.        | `[]`   |

### Persistence configuration

| Name                          | Description                                                              | Value          |
| ----------------------------- | ------------------------------------------------------------------------- | -------------- |
| `persistence.enabled`         | Enable a persistent volume for DATA_DIR. Disabling it loses all state on restart. | `true` |
| `persistence.existingClaim`   | Use an existing PVC instead of creating one. Leave empty to create one.  | `""`           |
| `persistence.storageClass`    | Storage class for the created PVC. Leave empty to use the cluster default. | `""`         |
| `persistence.accessMode`      | Access mode for the created PVC.                                          | `ReadWriteOnce` |
| `persistence.size`            | Size of the created PVC.                                                  | `1Gi`          |

### Application configuration

| Name                        | Description                                                                                                   | Value                 |
| --------------------------- | --------------------------------------------------------------------------------------------------------------| --------------------- |
| `config.publicBaseUrl`      | Where Google sends the user back after sign-in. Must be reachable at /oauth/callback. Required behind an ingress. | `""`               |
| `config.playlistPrivacy`    | Privacy of playlists this app creates. One of private, unlisted, public.                                      | `private`             |
| `config.quotaDailyLimit`    | The project's daily YouTube Data API quota. Only seeds the figure — the app's stored value wins after that.  | `10000`                |
| `config.quotaReserve`       | Quota units held back so a run never spends the last of the allocation on an insert it cannot confirm.       | `50`                   |
| `config.quotaResetTz`       | Timezone the daily quota resets in.                                                                           | `America/Los_Angeles` |
| `config.schedulerInterval`  | Seconds between checks of jobs marked "run it when credits allow".                                            | `60`                   |
| `config.insertDelay`        | Seconds paused between playlist inserts.                                                                       | `0.2`                  |

### Telegram notification configuration

| Name                     | Description                                                                              | Value |
| ------------------------ | ----------------------------------------------------------------------------------------- | ----- |
| `telegram.secretRef.name`| Existing secret name. Must have keys TELEGRAM_BOT_TOKEN and TELEGRAM_CHAT_ID.            | `""`  |

### Resource configuration

| Name        | Description                                                 | Value |
| ----------- | ----------------------------------------------------------- | ----- |
| `resources` | Configure resource requests and limits for the application. | `{}`  |

### Node Selector

| Name           | Description                     | Value |
| -------------- | -------------------------------- | ----- |
| `nodeSelector` | Define node selection criteria. | `{}`  |

### Tolerations

| Name          | Description                             | Value |
| ------------- | ---------------------------------------- | ----- |
| `tolerations` | Define tolerations for the application. | `[]`  |

### Affinity

| Name       | Description                                | Value |
| ---------- | -------------------------------------------- | ----- |
| `affinity` | Define affinity rules for the application. | `{}`  |

### Probes configuration

| Name                                   | Description                                                             | Value |
| --------------------------------------- | ------------------------------------------------------------------------| ----- |
| `probes.liveness.enabled`              | Enable or disable the liveness probe.                                   | `true` |
| `probes.liveness.path`                 | The path to check for the liveness probe.                               | `/`    |
| `probes.liveness.port`                 | The port used by the liveness probe.                                    | `http` |
| `probes.liveness.initialDelaySeconds`  | Initial delay before the first probe is initiated.                      | `10`   |
| `probes.liveness.periodSeconds`        | Period between successive probes.                                       | `30`   |
| `probes.liveness.timeoutSeconds`       | Timeout for each probe.                                                 | `5`    |
| `probes.liveness.failureThreshold`     | Number of consecutive failures before marking the probe as failed.      | `3`    |
| `probes.liveness.successThreshold`     | Number of consecutive successes before marking the probe as successful. | `1`    |
| `probes.readiness.enabled`             | Enable or disable the readiness probe.                                  | `true` |
| `probes.readiness.path`                | The path to check for the readiness probe.                              | `/`    |
| `probes.readiness.port`                | The port used by the readiness probe.                                   | `http` |
| `probes.readiness.initialDelaySeconds` | Initial delay before the first probe is initiated.                      | `5`    |
| `probes.readiness.periodSeconds`       | Period between successive probes.                                       | `15`   |
| `probes.readiness.timeoutSeconds`      | Timeout for each probe.                                                 | `5`    |
| `probes.readiness.failureThreshold`    | Number of consecutive failures before marking the probe as failed.      | `3`    |
| `probes.readiness.successThreshold`    | Number of consecutive successes before marking the probe as successful. | `1`    |
