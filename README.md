### Introduction

This repo shows the three common type of customizations that users typically make in Argo CD with the OpenShift GitOps operator. These three scenarios are:

1. Configuration Plugin via sidecar (plugin-sidecar folder)
2. Configuration plugin via legacy CMP (plugin-cmp)
3. Adding custom tooling (custom-tooling)

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

### Installation

To run one of the demos, go to the directory and run:

```
oc apply -k .
```