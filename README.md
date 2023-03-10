# Summary

The user "sundbp" reported an issue with Garden's Helm chart module. The problem was that Garden was not propagating the "crds" directory alongside the typical "templates" directory into the ".garden/build/" hierarchy. Because of this, the CRDs were not being installed. The user suggested that this is a bug because the "crds" directory is the standard way to install CRDs in Helm 3.

# Reproducing the issue

To reproduce the issue I used a tiny configuration to deploy a `helm chart` (Kong) that includes CRD's in the same way that sundbp described.

I was able to reproduce it and identified that the logic that collects things into ".garden/build/" only copies files and the "templates" directory, but no other directories. This seems like a bug because because the "crds" directory is the standard way to install CRDs in Helm 3.

# Setup

Change your local-kubernetes provider to `rancher-desktop` or `docker-desktop`

````
garden deploy
````

Then you will notice that the Kong CRD's were not installed as part of the deployment:

```
βπ€-π§shankyjs@ π» pop-os - π§± garden-charts-crdsmaster β4 β
βπ€-> k get crds
NAME                              CREATED AT
addons.k3s.cattle.io              2023-01-16T19:11:39Z
helmcharts.helm.cattle.io         2023-01-16T19:11:39Z
helmchartconfigs.helm.cattle.io   2023-01-16T19:11:39Z
```

If you check the .garden structure you will notice that the CRD's were not added to the .garden/build

````
βπ€-π§shankyjs@ π» pop-os - π§± kongmaster β4 β
βπ€-> tree
.
βββ charts
    βββ kong
        βββ CHANGELOG.md
        βββ Chart.lock
        βββ charts
        βΒ Β  βββ postgresql-11.9.13.tgz
        βββ Chart.yaml
        βββ FAQs.md
        βββ garden-values.yml
        βββ OWNERS
        βββ README.md
        βββ templates
        βΒ Β  βββ admission-webhook.yaml
        βΒ Β  βββ certificate.yaml
        βΒ Β  βββ config-dbless.yaml
        βΒ Β  βββ controller-rbac-resources.yaml
        βΒ Β  βββ custom-resource-definitions.yaml
        βΒ Β  βββ deployment.yaml
        βΒ Β  βββ extraManifests.yaml
        βΒ Β  βββ _helpers.tpl
        βΒ Β  βββ hpa.yaml
        βΒ Β  βββ ingress-class.yaml
        βΒ Β  βββ migrations-post-upgrade.yaml
        βΒ Β  βββ migrations-pre-upgrade.yaml
        βΒ Β  βββ migrations.yaml
        βΒ Β  βββ NOTES.txt
        βΒ Β  βββ pdb.yaml
        βΒ Β  βββ psp.yaml
        βΒ Β  βββ secret-sa-token.yaml
        βΒ Β  βββ service-account.yaml
        βΒ Β  βββ service-kong-admin.yaml
        βΒ Β  βββ service-kong-cluster-telemetry.yaml
        βΒ Β  βββ service-kong-cluster.yaml
        βΒ Β  βββ service-kong-manager.yaml
        βΒ Β  βββ service-kong-portal-api.yaml
        βΒ Β  βββ service-kong-portal.yaml
        βΒ Β  βββ service-kong-proxy.yaml
        βΒ Β  βββ service-kong-udp-proxy.yaml
        βΒ Β  βββ servicemonitor.yaml
        βΒ Β  βββ tests
        βΒ Β  βΒ Β  βββ test-jobs.yaml
        βΒ Β  βΒ Β  βββ test-resources.yaml
        βΒ Β  βββ wait-for-postgres-script.yaml
        βββ UPGRADE.md
        βββ values.yaml
````