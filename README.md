# HAMi - Heterogeneous AI Computing Virtualization Middleware

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Go Report Card](https://goreportcard.com/badge/github.com/Project-HAMi/HAMi)](https://goreportcard.com/report/github.com/Project-HAMi/HAMi)

HAMi (Heterogeneous AI Computing Virtualization Middleware) is a Kubernetes device plugin and scheduler extension that enables fine-grained sharing and isolation of heterogeneous AI accelerators (GPUs, NPUs, etc.) across workloads.

## Features

- **GPU Virtualization**: Share a single physical GPU among multiple pods with configurable memory and compute limits
- **Multi-vendor Support**: NVIDIA GPUs, Cambricon MLUs, Hygon DCUs, Iluvatar GPUs, and more
- **Resource Isolation**: Hard memory limits and compute core allocation per container
- **Kubernetes Native**: Works as a standard device plugin + scheduler extender
- **No Application Changes**: Transparent to existing GPU workloads

## Architecture

```
┌─────────────────────┐
│              Kubernetes API              │
└───────────────────┬─────────────────────┘
                    │
        ┌───────────▼───────────┐
        │   HAMi Scheduler      │
        │   (Extender)          │
        └───────────┬───────────┘
                    │
        ┌───────────▼───────────┐
        │   HAMi Device Plugin  │
        │   (Per Node)          │
        └───────────┬───────────┘
                    │
        ┌───────────▼───────────┐
        │   HAMi Hook Library   │
        │   (Per Container)     │
        └───────────────────────┘
```

## Quick Start

### Prerequisites

- Kubernetes >= 1.23
- NVIDIA drivers installed on GPU nodes
- `nvidia-container-toolkit` installed

### Installation

```bash
# Add HAMi Helm repository
helm repo add hami-charts https://project-hami.github.io/HAMi/
helm repo update

# Install HAMi
helm install hami hami-charts/hami \
  --namespace kube-system
```

### Usage

Request a fraction of a GPU in your pod spec:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
  - name: app
    image: nvidia/cuda:12.0-base
    resources:
      limits:
        nvidia.com/gpu: 1
        nvidia.com/gpumem: 4096    # 4 GiB GPU memory
        nvidia.com/gpucores: 50    # 50% GPU compute
```

## Supported Devices

| Vendor   | Device Type | Resource Name         |
|----------|-------------|----------------------|
| NVIDIA   | GPU         | `nvidia.com/gpu`     |
| Cambricon| MLU         | `cambricon.com/mlu`  |
| Hygon    | DCU         | `hygon.com/dcu`      |
| Iluvatar | GPU         | `iluvatar.ai/vgpu`   |
| Metax    | GPU         | `metax-tech.com/gpu` |

## Configuration

See [docs/configuration.md](docs/configuration.md) for full configuration options.

## Contributing

We welcome contributions! Please read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting pull requests.

1. Fork the repository
2. Create your feature branch (`git checkout -b feat/my-feature`)
3. Commit your changes following [Conventional Commits](https://www.conventionalcommits.org/)
4. Push to the branch and open a Pull Request

## License

HAMi is licensed under the [Apache License 2.0](LICENSE).

## Acknowledgements

This project is a fork of [Project-HAMi/HAMi](https://github.com/Project-HAMi/HAMi). We thank all original contributors for their work.
