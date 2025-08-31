# helm-service-base

## Overview

This repository contains a reusable Helm library chart for managing microservices in a Kubernetes cluster. It is published via GitHub Pages from the `gh-pages` branch using an automated workflow. Individual application charts should be maintained in their respective branches or repositories and use this library chart as a dependency.

## Usage as a dependency

This chart is intended to be consumed as a library dependency by application charts. Below are recommended ways to embed and use it.

1) Add as a dependency in your application's Chart.yaml (Helm v3):

```yaml
dependencies:
  - name: helm-service-base
    alias: base-service
    version: 0.0.5
    repository: "https://vlnrajesh.github.io/helm-service-base" # optional for published repos
```

2) Local development: use a local path in your application's Chart.yaml or use `helm dependency` commands.

- Local path example (useful for testing changes in the library chart):

```yaml
dependencies:
  - name: helm-service-base
    alias: base-service
    version: 0.0.3
    repository: "file://../helm-service-base"
```
### alias:

An alias allows a dependency to be included multiple times, giving each instance a unique name within the parent chart's scope.
During the dependency update and build process, the alias will be used to name the dependency directory.

After adding the dependency, run in the application chart directory:

```sh
helm dependency update
helm dependency build
```

These commands will populate the `charts/` directory inside your application chart with the packaged dependency.

## Consuming library helpers and templates

- Library charts provide template helpers (in templates/__helpers.tpl) that application charts can call with the `include` function. Check `templates/__helpers.tpl` to see the defined template names.

Example usage in an application template (adjust the include name to match the helper defined in this library):

```yaml
metadata:
  labels:
    {{- include "helm-service-base.labels" . | nindent 4 }}
```

## Passing configuration to the library chart

- Configure library behavior from your application's `values.yaml`. This chart reads values under the top-level keys `configmap` and `configmaps`.

Example `values.yaml` in your application chart:

```yaml
base-service:
  configmap:
    enabled: true
  configmaps:
    my-config:
      labels:
        environment: production
      data:
        key1: value1
```

## Template Documentation

### `templates/__helpers.tpl`
Reusable Helm template functions:
- **`..chart`**: Generates chart name and version label for resource identification.
- **`..labels`**: Generates common Kubernetes labels for resources, including chart, app, version, managed-by, selector labels, and app version if available.

### `templates/configmap.yaml`
Conditionally creates one or more ConfigMaps based on values provided in `values.yaml`:
- **Enable/Disable**: Controlled by `.Values.configmap.enabled` (boolean).
- **ConfigMaps**: Defined under `.Values.configmaps` as a map, where each key is the ConfigMap name and value is an object with optional `labels` and `data`.
- **Labels**: Custom labels can be added per ConfigMap.
- **Data**: Key-value pairs for ConfigMap data.

#### Example `values.yaml` configuration:
```yaml
configmap:
  enabled: true
configmaps:
  my-config:
    labels:
      environment: "production"
    data:
      key1: "value1"
      key2: "value2"
  another-config:
    data:
      foo: "bar"
```
This will create two ConfigMaps with the specified labels and data.

## Notes
- The `version` field in the application Chart.yaml dependency block should match the `version` in this chart's Chart.yaml if you want to use that packaged release. For local development you can use the `file://` repository syntax or package the chart locally with `helm package` and place the .tgz in your app's `charts/` folder.
- Library chart helpers and templates are intended to be reusable — inspect `templates/__helpers.tpl` and `templates/configmap.yaml` to understand available helpers and configuration keys.
- This chart is intended as a library and should not be deployed directly.
- Application charts should declare this chart as a dependency and use its templates and helpers.
