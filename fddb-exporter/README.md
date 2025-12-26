# Helm Chart for FDDB-Exporter
See: [application repository](https://github.com/itobey/fddb-exporter)

Usage:
```
helm pull oci://ghcr.io/itobey/charts/fddb-exporter --version 1.1.5
```

# Changelog

## 1.1.5

- Updated default app version to 1.7.0

## 1.1.4

- Updated default app version to 1.6.3

## 1.1.3

- Updated default app version to 1.6.2

## 1.1.2

- Updated default app version to 1.6.1

## 1.1.1

- Updated default app version to 1.6.0

## 1.1.0

- Added InfluxDB configuration
- Updated secret generation and reference for MongoDB and FDDB
- Added timezone configuration

## Parameters

### Replica Configuration

| Name           | Description                  | Value |
| -------------- | ---------------------------- | ----- |
| `replicaCount` | Number of replicas to deploy | `1`   |

### Image Configuration

| Name               | Description                       | Value                          |
| ------------------ | --------------------------------- |--------------------------------|
| `image.repository` | The image repository to pull from | `ghcr.io/itobey/fddb-exporter` |
| `image.tag`        | The image tag to pull             | `1.6.3`                        |
| `image.pullPolicy` | The Kubernetes image pull policy  | `IfNotPresent`                 |

### Name Overrides

| Name               | Description                                           | Value |
| ------------------ | ----------------------------------------------------- | ----- |
| `nameOverride`     | Partially override the name of the deployed resources | `""`  |
| `fullnameOverride` | Fully override the name of the deployed resources     | `""`  |

### FDDB configuration

| Name                       | Description                                      | Value                  |
|----------------------------|--------------------------------------------------|------------------------|
| `fddb.auth.username`       | FDDB username.                                   | `user@example.com`     |
| `fddb.auth.password`       | FDDB password.                                   | `password`             |
| `fddb.auth.secretRef.name` | Reference to the secret for FDDB authentication. | `fddb-exporter-secret` |

### MongoDB configuration

| Name                     | Description                                                              | Value       |
|--------------------------|--------------------------------------------------------------------------|-------------|
| `mongodb.enabled`        | Enables MongoDB.                                                         | `true`      |
| `mongodb.host`           | MongoDB host.                                                            | `localhost` |
| `mongodb.port`           | MongoDB port.                                                            | `27017`     |
| `mongodb.database`       | MongoDB database name.                                                   | `database`  |
| `mongodb.username`       | MongoDB username.                                                        | `username`  |
| `mongodb.password`       | MongoDB password.                                                        | `password`  |
| `mongodb.secretRef.name` | Existing MongoDB secret name. Must have key SPRING_DATA_MONGODB_PASSWORD | `password`  |

### InfluxDB configuration

| Name                      | Description                                                               | Value                   |
|---------------------------|---------------------------------------------------------------------------|-------------------------|
| `influxdb.enabled`        | Enables InfluxDB.                                                         | `false`                 |
| `influxdb.url`            | InfluxDB url.                                                             | `http://localhost:8086` |
| `influxdb.org`            | InfluxDB org.                                                             | `primary`               |
| `influxdb.bucket`         | InfluxDB bucket.                                                          | `fddb-exporter`         |
| `influxdb.token`          | InfluxDB token.                                                           | `token`                 |
| `influxdb.secretRef.name` | Existing InfluxDB secret name. Must have key FDDB_EXPORTER_INFLUXDB_TOKEN | `password`              |

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
| `service.port` | The port that the service will expose.                                   | `8080`      |

### Ingress configuration

| Name                 | Description                               | Value  |
| -------------------- | ----------------------------------------- | ------ |
| `ingress.enabled`    | Enable or disable the ingress.            | `true` |
| `ingress.className`  | The class name of the ingress controller. | `""`   |
| `ingress.hosts.host` | Host name of the ingress.                 |        |
| `ingress.tls`        | TLS configuration for the ingress.        | `[]`   |

### Resource configuration

| Name        | Description                                                 | Value |
| ----------- | ----------------------------------------------------------- | ----- |
| `resources` | Configure resource requests and limits for the application. | `{}`  |

### Node Selector

| Name           | Description                     | Value |
| -------------- | ------------------------------- | ----- |
| `nodeSelector` | Define node selection criteria. | `{}`  |

### Tolerations

| Name          | Description                             | Value |
| ------------- | --------------------------------------- | ----- |
| `tolerations` | Define tolerations for the application. | `[]`  |

### Affinity

| Name       | Description                                | Value |
| ---------- | ------------------------------------------ | ----- |
| `affinity` | Define affinity rules for the application. | `{}`  |

### Probes configuration

| Name                                   | Description                                                             | Value                        |
| -------------------------------------- | ----------------------------------------------------------------------- | ---------------------------- |
| `probes.liveness.enabled`              | Enable or disable the liveness probe.                                   | `true`                       |
| `probes.liveness.path`                 | The path to check for the liveness probe.                               | `/actuator/health/liveness`  |
| `probes.liveness.port`                 | The port used by the liveness probe.                                    | `http`                       |
| `probes.liveness.initialDelaySeconds`  | Initial delay before the first probe is initiated.                      | `25`                         |
| `probes.liveness.periodSeconds`        | Period between successive probes.                                       | `600`                        |
| `probes.liveness.timeoutSeconds`       | Timeout for each probe.                                                 | `5`                          |
| `probes.liveness.failureThreshold`     | Number of consecutive failures before marking the probe as failed.      | `3`                          |
| `probes.liveness.successThreshold`     | Number of consecutive successes before marking the probe as successful. | `1`                          |
| `probes.readiness.enabled`             | Enable or disable the readiness probe.                                  | `true`                       |
| `probes.readiness.path`                | The path to check for the readiness probe.                              | `/actuator/health/readiness` |
| `probes.readiness.port`                | The port used by the readiness probe.                                   | `http`                       |
| `probes.readiness.initialDelaySeconds` | Initial delay before the first probe is initiated.                      | `20`                         |
| `probes.readiness.periodSeconds`       | Period between successive probes.                                       | `30`                         |
| `probes.readiness.timeoutSeconds`      | Timeout for each probe.                                                 | `5`                          |
| `probes.readiness.failureThreshold`    | Number of consecutive failures before marking the probe as failed.      | `3`                          |
| `probes.readiness.successThreshold`    | Number of consecutive successes before marking the probe as successful. | `1`                          |

### Prometheus PodMonitor configuration

| Name                       | Description                                  | Value      |
| -------------------------- | -------------------------------------------- | ---------- |
| `podMonitor.enabled`       | Enable or disable the PodMonitor.            | `false`    |
| `podMonitor.interval`      | Interval at which metrics should be scraped. | `30s`      |
| `podMonitor.scrapeTimeout` | Timeout for the scrape request.              | `10s`      |
| `podMonitor.path`          | Path to scrape metrics from.                 | `/metrics` |
