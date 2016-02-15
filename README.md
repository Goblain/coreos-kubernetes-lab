# Simple and dirty CoreOS/Kubernetes lab

## Requirements
* vagrant
* ansible
* ansible-galaxy install debops.pki

## Start environment
```
vagrant up
./k8s-provision
```
## Use cluster
after a short while when cluster is provisioned you can use it like :
```
wget https://storage.googleapis.com/kubernetes-release/release/v{{version}}/bin/linux/amd64/kubectl
./kubectl --kubeconfig kube.conf get nodes
```
or with a dockerized kubectl
```
alias kubectl='docker run -it --rm -v $(pwd):/wrk -w /wrk --net host goblain/kubectl:{{version}}'
kubectl --kubeconfig kube.conf get nodes
```
