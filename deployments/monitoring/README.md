# monitoring

SQuaRE packaging of monitoring apps

## Requirements

| Repository | Name | Version |
|------------|------|---------|
| https://helm.influxdata.com/ | chronograf | 1.2.5 |
| https://helm.influxdata.com/ | influxdb2 | 2.0.12 |

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| chronograf.env.BASE_PATH | string | `"/chronograf"` |  |
| chronograf.env.CUSTOM_AUTO_REFRESH | string | `"1s=1000"` |  |
| chronograf.env.GH_CLIENT_ID | string | `"a12e66e2a1d213b2bb17"` |  |
| chronograf.env.GH_ORGS | string | `"lsst-sqre"` |  |
| chronograf.env.HOST_PAGE_DISABLED | bool | `true` |  |
| chronograf.env.INFLUXDB_ORG | string | `"square"` |  |
| chronograf.env.INFLUXDB_URL | string | `"https://monitoring.lsst.codes"` |  |
| chronograf.envFromSecret | string | `"monitoring"` |  |
| chronograf.image.pullPolicy | string | `"IfNotPresent"` |  |
| chronograf.image.tag | string | `"1.9.4"` |  |
| chronograf.ingress.enabled | bool | `false` |  |
| chronograf.oauth.enabled | bool | `false` |  |
| chronograf.persistence.enabled | bool | `true` |  |
| chronograf.persistence.size | string | `"20Gi"` |  |
| chronograf.resources.limits.cpu | int | `4` |  |
| chronograf.resources.limits.memory | string | `"32Gi"` |  |
| chronograf.resources.requests.cpu | int | `1` |  |
| chronograf.resources.requests.memory | string | `"1024Mi"` |  |
| chronograf.service.replicas | int | `1` |  |
| chronograf.service.type | string | `"ClusterIP"` |  |
| chronograf.updateStrategy.type | string | `"Recreate"` |  |
| cronjob.image | object | `{"repository":"ghcr.io/lsst-sqre/influx-bucket-mapper","tag":"0.1.3"}` | image for bucket-mapping cronjob |
| cronjob.image.repository | string | `"ghcr.io/lsst-sqre/influx-bucket-mapper"` | repository for bucket-mapper |
| cronjob.image.tag | string | `"0.1.3"` | tag for bucket-mapper |
| influxdb2.adminUser | object | `{"bucket":"monitoring","existingSecret":"monitoring","organization":"square","retention_policy":"30d"}` | InfluxDB2 admin user; uses admin-password/admin-token keys from secret. |
| influxdb2.ingress | object | `{"enabled":false}` | InfluxDB2 ingress configuration. |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.5.0](https://github.com/norwoodj/helm-docs/releases/v1.5.0)
