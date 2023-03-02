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
┌🤘-🐧shankyjs@ 💻 pop-os - 🧱 garden-charts-crdsmaster ⌀4 ✗
└🤘-> k get crds
NAME                              CREATED AT
addons.k3s.cattle.io              2023-01-16T19:11:39Z
helmcharts.helm.cattle.io         2023-01-16T19:11:39Z
helmchartconfigs.helm.cattle.io   2023-01-16T19:11:39Z
```

If you check the .garden structure you will notice that the CRD's were not added to the .garden/build

````
┌🤘-🐧shankyjs@ 💻 pop-os - 🧱 kongmaster ⌀4 ✗
└🤘-> tree
.
└── charts
    └── kong
        ├── CHANGELOG.md
        ├── Chart.lock
        ├── charts
        │   └── postgresql-11.9.13.tgz
        ├── Chart.yaml
        ├── FAQs.md
        ├── garden-values.yml
        ├── OWNERS
        ├── README.md
        ├── templates
        │   ├── admission-webhook.yaml
        │   ├── certificate.yaml
        │   ├── config-dbless.yaml
        │   ├── controller-rbac-resources.yaml
        │   ├── custom-resource-definitions.yaml
        │   ├── deployment.yaml
        │   ├── extraManifests.yaml
        │   ├── _helpers.tpl
        │   ├── hpa.yaml
        │   ├── ingress-class.yaml
        │   ├── migrations-post-upgrade.yaml
        │   ├── migrations-pre-upgrade.yaml
        │   ├── migrations.yaml
        │   ├── NOTES.txt
        │   ├── pdb.yaml
        │   ├── psp.yaml
        │   ├── secret-sa-token.yaml
        │   ├── service-account.yaml
        │   ├── service-kong-admin.yaml
        │   ├── service-kong-cluster-telemetry.yaml
        │   ├── service-kong-cluster.yaml
        │   ├── service-kong-manager.yaml
        │   ├── service-kong-portal-api.yaml
        │   ├── service-kong-portal.yaml
        │   ├── service-kong-proxy.yaml
        │   ├── service-kong-udp-proxy.yaml
        │   ├── servicemonitor.yaml
        │   ├── tests
        │   │   ├── test-jobs.yaml
        │   │   └── test-resources.yaml
        │   └── wait-for-postgres-script.yaml
        ├── UPGRADE.md
        └── values.yaml
````