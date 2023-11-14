### Introduction

This repo shows the three common type of customizations that users typically make in Argo CD with the OpenShift GitOps operator. These three scenarios are:

1. [Configuration Plugin via sidecar](https://argo-cd.readthedocs.io/en/stable/operator-manual/config-management-plugins/#sidecar-plugin) (plugin-sidecar folder)
2. [Configuration plugin via legacy CMP](https://argo-cd.readthedocs.io/en/stable/operator-manual/config-management-plugins/#configmap-plugin) (plugin-cmp) (Not Available in Argo CD 2.8 and later)
3. [Adding custom tooling](https://argo-cd.readthedocs.io/en/stable/operator-manual/custom_tools/) (custom-tooling)

All examples assume that the OpenShift GitOps operator has already been installed. Each example is self-container and will deploy a
new Argo CD instance along with a demo application in a new namespace making it easy to clean things up after you are done and not impacting
other workloads on the cluster.

### Plugins

The two plugin examples demo the same scenario where we have a cluser wide environment variable to represent
the environment of the cluster (dev, test, prod) that applications can use to configure themselves appropriately. For example
with a Quarkus application the environment variable could be used as a profile to drive different behaviors and settings
for `dev` versus `prod`.

Another scenario where this can be used is when we need to configure things that are dependent on a setting that is specific
to a cluster, for example CLUSTER_ID. While best practice would be to parameterize this in git, either as a kustomize overlay or a helm
values.yaml, this isn't always feasible like when working with ephemeral clusters or a fleet of hundreds of clusters.

So in the plugin example we will set a system wide environment variable on the repo server, APP_ENV, and have a plugin replace
the environment variable ${APP_ENV} if it appears in any of the manifests. If you look at the deploy.yaml file for the application
you will see we define an environment variable where we want this substitution to take place:

```
    spec:
      containers:
      - env:
        - name: APP_ENV
          value: ${APP_ENV}
```

The plugin will replace `$(APP_ENV)` with `prod` which has been defined as an environment variable in the repo server.

After installing one of the plugin demos, validate that the `demo-plugin` deployment has the environment variable `APP_ENV` set to `prod`

### Custom Tooling

Custom tooling can be used to replace the versions of the tools included in Argo CD (i.e helm, kustomize, etc) with alternative versions or
to provide new tools.

In this demo we replace kustomize with a version included in the tools container which is v5.1.0 whereas the kustomize included in OpenShift
GitOps is v5.0.1. To confirm that the demo worked, rsh into the repo-server pod and run `kustomize version`.

### Installation

To run one of the demos, `cd` to the demo directory you want and run:

```
oc apply -k .
```
