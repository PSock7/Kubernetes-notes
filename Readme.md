# RBAC

* Create user with certificate 
1. mkdir cert && cd cert
2. openssl genrsa -out <name>.key 2048 
3. openssl req -new -key <name>.key -out <name>.csr -subj "/CN=<name>/O=<groupname>"
4. openssl x509 -req -in <name>.csr -CA /minikube/ca.crt -CAkey /minikube/ca.key -CAcreateserial -out <name>.crt -days <numberofdays>
5. kubectl config set-credentials <name> --client-certificate=<name>.crt --client-key=<name>.key
6. kubectl config set-context <nameofcontext> --cluster=minikube --user=<name> --namespace=default
7. kubectl config use-context <nameofcontext>
8. Kubectl config get-context #view the current user 

# Kubeadmin 
## initalise the control plane
1. sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=192.168.56.10 <ip_of_the_server/machine>
2. mkdir -p $HOME/.kube
3. sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
. sudo chown $(id -u):$(id -g) $HOME/.kube/config
4. retrieve join command with : kubeadm token create --print-join-command

## upgrade kubeadm version
1. kubeadm upgrade plan
2. kubeadm upgrade apply v1.19.0 or <version_number>
3. kubectl drain est utilisée pour vider un nœud de tous ses workloads : kubectl drain kube-control-plane --ignore-daemonsets
4. update the kubelet and 
kubectl command depending the os that you have
5. kubectl uncordon kube-control-plane

kubectl drain : Vide un nœud de ses workloads (sauf les daemonsets).
kubectl uncordon : Restaure la capacité du nœud à accepter de nouveaux pods

## upgrade worker node
1. sudo kubeadm upgrade node
2. kubect drain worker --ignore-daemonsets
3. kubectl uncordon worker

## Backing Up and restoring etcd
sudo ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/etcd-backup.db

## Restoring etcd
sudo ETCDCTL_API=3 etcdctl --data-dir=/var/lib/from-backup snapshot restore \/opt/etcd-backup.db

