# Helm Templates

This repository stores Helm values templates and deployment notes for several Kubernetes components.

## Repository Layout

### `monitoring/`

- `grafana.yaml`: current Grafana values template
- `grafana_legacy.yaml`: older Grafana values template kept for reference
- `loki.yaml`: Loki values template
- `prometheus.yaml`: Prometheus values template
- `promtail.yaml`: Promtail values template
- `elasticsearch.yaml`: Elasticsearch values template
- `elasticsearch.md`: Elasticsearch installation and backup notes
- `oidc.md`: Grafana OIDC setup notes

### `database/`

- `mongodb.yaml`: MongoDB values template
- `postgresql.yaml`: PostgreSQL values template

### `gitops/`

- `argocd.yaml`: Argo CD values template
- `argocd_full.yaml`: expanded or alternate Argo CD values template
- `gitlabrunner.yaml`: GitLab Runner values template
- `test_runner.yaml`: test runner related values template

## Purpose

These YAML files are values files intended to be passed to upstream Helm charts with `-f`.

This repository is not a standalone Helm chart:

- there is no `Chart.yaml`
- the files here are template inputs for upstream charts
- some directories also include operational notes in Markdown

## Usage

Use the appropriate file as the values input for `helm upgrade --install`.

Examples:

```bash
helm upgrade --install grafana grafana/grafana -f monitoring/grafana.yaml
```

```bash
helm upgrade --install loki grafana/loki -f monitoring/loki.yaml
```

```bash
helm upgrade --install prometheus prometheus-community/prometheus -f monitoring/prometheus.yaml
```

```bash
helm upgrade --install promtail grafana/promtail -f monitoring/promtail.yaml
```

```bash
helm upgrade --install elasticsearch elastic/elasticsearch -f monitoring/elasticsearch.yaml
```

Use the same pattern for the database and GitOps templates once you confirm the target chart and version for your environment.

## Documentation Notes

Additional component-specific notes are included here:

- [monitoring/elasticsearch.md](monitoring/elasticsearch.md): Elasticsearch install, persistence, and snapshot backup notes
- [monitoring/oidc.md](monitoring/oidc.md): Grafana Generic OAuth / OIDC setup notes

## Secrets

Do not commit real passwords, tokens, client secrets, or API keys into these YAML files.

Prefer Kubernetes Secrets and reference them from the values files where supported. For example, in the current Grafana template the main secret-related keys are:

- `admin.existingSecret`
- `envFromSecret`
- `envFromSecrets`
- `ldap.existingSecret`
- `smtp.existingSecret`

This keeps sensitive data outside Git while still allowing Helm to wire the application to the required credentials.

## What To Review Before Deploying

These files are starting points, not finished environment configs. Review at least:

- ingress hostnames and TLS settings
- storage class and persistence size
- image tags or chart versions
- service exposure
- resource requests and limits
- secret references
- authentication settings such as OIDC, LDAP, or SMTP where relevant

For Grafana specifically, review:

- `ingress.enabled`
- `ingress.hosts`
- `ingress.tls`
- `persistence.enabled`
- `persistence.size`
- `admin.existingSecret`
- `envFromSecret`
- `envFromSecrets`
- `smtp.existingSecret`
- `ldap.enabled`
- `ldap.existingSecret`
- `image.tag`

## Notes

- `monitoring/grafana.yaml` includes inline comments on keys that are expected to be set explicitly later.
- In the current Grafana template, `grafana.ini.server.domain` is derived from the first ingress host when ingress is enabled.
- Chart versions are not pinned consistently in this repository, so confirm upstream chart compatibility before applying changes.
