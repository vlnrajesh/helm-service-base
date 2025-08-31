# helm-service-base

## Overview

This repository contains a reusable Helm library chart for managing microservices in a Kubernetes cluster. It is published via GitHub Pages from the `gh-pages` branch using an automated workflow. Individual application charts should be maintained in their respective branches or repositories and use this library chart as a dependency.

## Usage

Add this chart as a dependency in your application chart's `Chart.yaml`:
```yaml
dependencies:
  - name: helm-service-base
    version: 0.0.3
    repository: https://vlnrajesh.github.io/helm-service-base
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
- This chart is intended as a library and should not be deployed directly.
- Application charts should declare this chart as a dependency and use its templates and helpers.
