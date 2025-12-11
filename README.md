# Talos Runtime

[file-issues]: https://github.com/sommerfeld-io/talos-runtime/issues
[project-board]: https://github.com/orgs/sommerfeld-io/projects/1/views/1

A reproducible local development environment for Kubernetes using Talos Linux running in Docker containers inside a dedicated Ubuntu Virtual Machine managed with Vagrant. Inside the VM, Docker runs several Talos node containers (control-plane and workers). ArgoCD Autopilot runs inside the cluster to provide GitOps deployment of manifests stored in a remote repository.

- [Sonarcloud Code Quality and Security Analysis](https://sonarcloud.io/project/overview?id=sommerfeld-io_talos-runtime)
- [Where to file issues][file-issues]
- [Project Board for Issues and Pull Requests][project-board]

## Why run the cluster in a VM?

- Clean host environment: no conflicts with any installation on the primary OS
- Disposable environment: destroy and recreate the VM + cluster quickly
- Resource control: allocate CPU/RAM to the VM and avoid interfering with host usage
- Reusable infrastructure: the same provisioning code can later be applied to bare-metal or cloud VMs

## Recommended Node Sizing (single VM hosting all nodes)

| Node Type | Count | RAM (each) | CPU (each) | Purpose |
|---|---:|---:|---:|---|
| Control Plane | 1 | 2–4 GB | 2 vCPUs | Kubernetes control plane, etcd, Talos API |
| Worker (general) | 2 | 2 GB | 1 vCPU | Application workloads |
| Worker (management) | 1 | 3 GB | 2 vCPUs | ArgoCD, Prometheus, Grafana, Loki, etc. |

> **:bulb: NOTE:** These are conservative recommendations targeted at local development. The VM must have enough resources to host all Talos node containers.

## Prerequisites

- On the host machine:
    - VirtualBox and Vagrant installed and configured
    - Ansible for provisioning inside the VM
    - Task ([taskfile.dev](https://taskfile.dev)) as the main command runner
- On the guest VM (the VM created by Vagrant):
    - None, everything is installed and configured by Ansible during provisioning

## Usage

Use the `taskfile.yml` from the project root of this repository to start, stop, destroy the Vagrantbox.

To interact with the Talos cluster, `ssh` into the VM and use the `taskfile.yml` from inside the VM. Or use `talosctl` and `kubectl` inside the VM directly.

## Deployment Flow (GitOps with ArgoCD)

1. Developer edits Kubernetes manifests locally and pushes commits to a Git repo.
2. ArgoCD Autopilot running in the Talos cluster polls the Git repo and detects changes.
3. ArgoCD syncs the cluster to match the repository state and applies the manifests.
4. Branch-based workflows: ArgoCD can be configured to watch branches and deploy each branch into separate namespaces for isolated testing.

This means you do not need to run `kubectl apply` from the host for normal development workflows — commit and push is the delivery mechanism. In a non-disposable environment, this should be the way to go.

But since this is a disposable environment for development, you can also run `kubectl apply` directly against the cluster. When the VM gets cluttered, just destroy and recreate it.

## Risks and Technical Debts

All issues labeled as `risk` (= some sort of risk or a technical debt) or `security` (= disclosed security issues - e.g. CVEs) [are tracked as GitHub issue](https://github.com/sommerfeld-io/talos-runtime/issues?q=is%3Aissue+label%3Asecurity%2Crisk+is%3Aopen) and carry the respective label.

## Contact

Feel free to contact me via <sebastian@sommerfeld.io> or [raise an issue in this repository][file-issues].
