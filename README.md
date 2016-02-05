== Requirements ==
* vagrant
* ansible

== Start environment ==
vagrant up
./k8s-provision

== Use cluster ==
after a short while when cluster is provisioned you can use it like :

wget https://storage.googleapis.com/kubernetes-release/release/v{{version}}/bin/linux/amd64/kubectl
./kubectl -s 172.17.8.11:8080 get nodes

or with a dockerized kubectl

alias kubectl='docker run -it --rm -v $(pwd):/wrk -w /wrk --net host goblain/kubectl:{{version}}'
kubectl -s 172.17.8.11:8080 get nodes

