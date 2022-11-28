# Grafana NOperator

**This is not a Grafana Operator.**

This is repo will deploy a completly stateless Grafana pod in your cluster with its dashboards configured as ConfigMaps.

Whenever you are adding dashboards as ConfigMaps, a pod reboot is required to make them avaiable in Grafana's UI.

## How does it works

This setup uses an `initContainer` to fetch the existing ConfigMaps annoted as `metadata.labels.grafana/scrape: dashboard` in the current namespace.

Those dashboards are mounted in an `emptyDir{}` volume which is then used as the dashboards' source in Grafana's container.

## Installation

Default installation will use a `grafana-noperator` namespace:

```bash
# Deploy grafana instance
kubectl -f https://github.com/William-LP/grafana_noperator

# Deploy basic dashboards
kubectl -f https://github.com/William-LP/grafana_noperator/dashboards

# Restart grafana pod
kubectl delete pod -l app.kubernetes.io/component=grafana -n grafana-noperator

# Check dashboards exist in /grafana-dashboard-definitions/0
kubectl exec -it deploy/grafana -c grafana -- bash -c "ls /grafana-dashboard-definitions/0/*"
```

## To do

- [ ] Sateless configuration for data source
- [ ] Set default admin password in config (CRITIAL)
- [ ] Rework config out of `config.yaml`
- [ ] Create some nice dashboards as default
- [ ] Create some alternative install method (kustomize / helm)
- [ ] Remove hardcoded namespace in `initContainer` script
