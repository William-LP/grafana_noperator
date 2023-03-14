# Grafana NOperator

**This is not a Grafana Operator.**

This is repo will deploy a completly stateless Grafana deployment in your cluster with its dashboards configured as ConfigMaps and without the uses of CRD.

This has been created for fun but can still be used in production. 

This only downside is whenever you are adding dashboards as ConfigMaps, a pod reboot is required to make them avaiable in Grafana's UI.

## How does it works

This setup uses an `initContainer` to fetch the existing ConfigMaps annoted as `metadata.labels.grafana/scrape: dashboard` in the current namespace.

Those dashboards are mounted in an `emptyDir{}` volume which is then used as the dashboards' source in Grafana's container.

## Installation

Default installation will use a `grafana-noperator` namespace:

```bash
# Download project
git clone https://github.com/William-LP/grafana_noperator && cd grafana_noperator

# Deploy grafana instance
kubectl apply -f setup/

# Deploy basic dashboards
kubectl apply -f dashboards/

# Restart grafana pod
kubectl delete pod -l app.kubernetes.io/component=grafana -n grafana-noperator

# Check dashboards exist in /grafana-dashboard-definitions/0
kubectl exec -it deploy/grafana -c grafana -- bash -c "ls /grafana-dashboard-definitions/0/*"
```

## To do

- [ ] Reboot pod automatically if new dashboards to be added
- [ ] Sateless configuration for data source
- [ ] Set default admin password in config (CRITIAL)
- [ ] Rework config out of `config.yaml`
- [ ] Create some nice dashboards as default
- [ ] Create some alternative install method (kustomize / helm)
- [ ] Remove hardcoded namespace in `initContainer` script
