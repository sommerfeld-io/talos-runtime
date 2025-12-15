# Role: helm

Install `helm`. For the time being, this role is used in the `talos` repository only. This repo provides a local development environment for Kubernetes using Talos.

Even though usage is limited at the moment, this role supports all machines (RasPi and workstation).

## Expected Variables

The following variables are expected to be set globally or to be passed to the role:

| Variable             | Description |
|----------------------|-------------|
| `{{ ansible_user }}` | `The user to install and configure for (typically the logged-in user).` |

## Optional Variables

The following variables are optional and have default values:

| Variable                              | Description | Default |
|---------------------------------------|-------------|---------|
| `{{ common_architecture }}` | Install for desktop (`amd64`) or Raspberry Pi (`arm64`). | `amd64` |
| `{{ common_version }}`      | Version of `helm` to install. | `common_v4.0.4` |
