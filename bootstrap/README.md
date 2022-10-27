
We use argocd to sync cluster state with the configuration specified in knative/test-infra.


## Bootstrapping the cluster

1. Install the CNI

```
helm upgrade --install cilium cilium/cilium --version 1.12.3 \
  --namespace kube-system \
  --set nodeinit.enabled=true \
  --set nodeinit.reconfigureKubelet=true \
  --set nodeinit.removeCbrBridge=true \
  --set cni.binPath=/home/kubernetes/bin \
  --set gke.enabled=true \
  --set ipam.mode=kubernetes \
  --set hubble.relay.enabled=true \
  --set hubble.ui.enabled=true \
  --set ipv4NativeRoutingCIDR=10.201.64.0/18 \
  --set agentNotReadyTaintKey=ignore-taint.cluster-autoscaler.kubernetes.io/cilium-agent-not-ready
```

1. Install Istio

```
k create ns istio-operator && k create ns istio-operator && k apply -f istio/
```

Subsequent changes to Istio are handled by Argocd

1. Install ArgoCD

```
k create ns argocd
k label ns argocd istio-injection=enabled
k apply -n argocd -k bootstrap/
```
