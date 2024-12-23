# Install on MicroK8s

## Pre-requisites
- [MicroK8s](https://microk8s.io/)
- To install etcd using these instructions, you must have [Helm](https://helm.sh) in your client environment.
- [Download the Compose on Kubernetes installer](https://github.com/docker/compose-on-kubernetes/releases).
- Ensure storage and dns are enabled: `microk8s.enable storage dns`
- `kubectl` availabe in path - if it's not already available it can be aliased from the snap with: `sudo snap alias microk8s.kubectl kubectl`.

## Create compose namespace

- Confirm that MicroK8s is working with `microk8s.inspect`.
- Create a compose namespace by running `microk8s.kubectl create namespace compose`.

## Populate Kubernetes Config

If you do not already have a `~/.kube/config` file create it with `microk8s.kubectl config view --raw > ~/.kube/config`.

## Deploy etcd

Compose on Kubernetes requires an etcd instance (in addition to the kube-system etcd instance). Please follow [How to deploy etcd](./deploy-etcd.md).

## Deploy Compose on Kubernetes

Run `./installer-[darwin|linux|windows.exe] -namespace=compose -etcd-servers=http://compose-etcd-client:2379`.

## Deploy a stack in the cluster

By now you should be able to [Check that Compose on Kubernetes is installed](../README.md#check-that-compose-on-kubernetes-is-installed):

```bash
$ microk8s.kubectl api-versions | grep compose
compose.docker.com/v1beta1
compose.docker.com/v1beta2
```

If the APIs are visible it should be possible to [Deploy a stack](../README.md#deploy-a-stack).

If everything has worked the `web-published` service should be visible from `kubectl` e.g:

```
$ kubectl get services
NAME            TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)           AGE
db              ClusterIP      None             <none>        55555/TCP         3m31s
kubernetes      ClusterIP      10.152.183.1     <none>        443/TCP           33m
web             ClusterIP      None             <none>        55555/TCP         3m31s
web-published   LoadBalancer   10.152.183.180   <pending>     33000:30218/TCP   3m31s
words           ClusterIP      None             <none>        55555/TCP         3m31s
```

In this case it's possible to open <http://10.152.183.180:33000/> in the browser, but your Cluster IP will most likely be different.