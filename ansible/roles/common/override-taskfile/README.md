# Role: override-taskfile

Override the default `taskfile.yml` with a `taskfile.yml` that takes care of starting, stopping and interacting with the Talos cluster.

This role is used in the `talos` repository only.

## Expected Variables

The following variables are expected to be set globally or to be passed to the role:

| Variable             | Description |
|----------------------|-------------|
| `{{ ansible_user }}` | `The user to install and configure for (typically the logged-in user).` |
