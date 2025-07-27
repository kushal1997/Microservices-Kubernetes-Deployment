# Metrics Server Setup on Docker Desktop Kubernetes

This guide explains how to install and configure the Kubernetes `metrics-server` for use on Docker Desktop.

## Prerequisites

- Docker Desktop installed and running
- Kubernetes enabled in Docker Desktop
- `kubectl` installed and configured to use the `docker-desktop` context

Check current context:

```bash
kubectl config current-context
```

It should return:

```
docker-desktop
```

## Installation Steps

### 1. Deploy Metrics Server

Apply the official components manifest:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

### 2. Patch for Docker Desktop Compatibility

Docker Desktop uses self-signed certificates, which can cause issues. Patch the deployment to allow insecure TLS:

```bash
kubectl patch deployment metrics-server -n kube-system \
  --type='json' \
  -p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'
```

### 3. Verify Installation

Check if the metrics server pod is running:

```bash
kubectl get pods -n kube-system | grep metrics-server
```

You should see a `Running` status with `1/1` ready.

### 4. Test Metrics Server

Run the following to view resource usage:

```bash
kubectl top nodes
kubectl top pods -A
```

## Uninstallation

To remove the metrics server:

```bash
kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

## References

- [Metrics Server GitHub](https://github.com/kubernetes-sigs/metrics-server)
