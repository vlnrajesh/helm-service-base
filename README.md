# helm-service-base

## Template Documentation

### `templates/__helpers.tpl`
This file defines reusable Helm template functions:
- **`..chart`**: Generates a chart name and version label, used for resource identification.
- **`..labels`**: Generates common Kubernetes labels for resources, including chart, app, version, and managed-by. It also includes selector labels and app version if available.

### `templates/configmap.yaml`
This template conditionally creates one or more ConfigMaps based on values provided in `values.yaml`:
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

## Unit Testing with helm-unittest

Unit tests for templates are placed in the `tests/` directory. The chart uses [helm-unittest](https://github.com/quintush/helm-unittest) for testing template rendering.

### How to run unit tests

1. Install helm-unittest plugin (if not already installed):
   ```sh
   helm plugin install https://github.com/quintush/helm-unittest
   ```
2. Run the tests:
   ```sh
   helm unittest .
   ```
   This will execute all test suites in the `tests/` directory and report results.

### Example test file: `tests/configmap_test.yaml`

This file contains test cases for the ConfigMap template, including:
- Rendering when `configmap.enabled` is false (no ConfigMap should be created)
- Rendering multiple ConfigMaps with custom labels and data
- Edge cases for missing labels/data

Refer to the file for more details and add your own cases as needed.
