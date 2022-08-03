# Opencast Grafana & Monitoring Collection

This repo contains some basic examples for monitoring tasks around [Opencast](https://opencast.org/), using the Grafana ecosystem.

## Grafana Dashboard

The file `opencast-metrics-dashboard.json.j2` contains a basic [Grafana](https://grafana.com/grafana/) dashboard for Opencast.

The dashboard uses the `label_replace()` function a lot, e.g. in the form of `label_replace(your_metric, \"instance\", \"$1\", \"instance\", \"(.*).example.com.*\")`. Those variables have been templated here like this:

| variable name | example value |
|:--|:--|
| `your_domain_regex` | `(.*).example.com.*` |
| `your_worker_regex` | `https://oc-worker[0-9]{2}.example.com` |
| `your_admin_regex` | `oc-admin.example.com.de.*` |
| `your_presentation_regex` | `oc-presentation.example.com.de.*` |

## Loki Promtail Config

The file `opencast_promtail_config.yml.j2` contains a basic logging config for [promtail](https://grafana.com/docs/loki/latest/clients/promtail/) with Opencast.
It can directly be fed to the ansible role [monitoring_promtail](https://galaxy.ansible.com/elan/monitoring_promtail) via the variable `promtail_config_template`.

The following variables are somehow expected by this template, the default values refer to the ones set by the ansible role.

| variable name | (default-)value |
|:--|:--|
| `inventory_hostname` | required, the current host, e.g. `oc-admin.example.com` |
| `loki_host` | required |
| `loki_http_listen_port` | required |
| `promtail_use_tls` | `false` |
| `promtail_http_listen_port` | `9080` |
| `promtail_grpc_listen_port` | `0` |
| `use_basic_auth` | `false` |
| `basic_auth_user` | required if  `use_basic_auth` is `true` |
| `basic_auth_password` | required if  `use_basic_auth` is `true` |
| `promtail_job_journal` | `false` |

## Opencast Prometheus Config

The file `opencast-prometheus.yml` contains an example for prometheus scrape jobs that expands on the example from the [docs](https://docs.opencast.org/develop/admin/#modules/metrics/) for a multi-node cluster.
Specifically, it splits up the metrics from the opencast endpoint into two jobs:
  1. Opencast-specific metrics that are only scraped from the admin node
  2. JVM metrics that are scraped from all nodes
