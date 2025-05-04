# RBAC

## Create user with certificate

```sh
mkdir cert && cd cert
```

```sh
openssl genrsa -out <name>.key 2048 
```

```sh
openssl req -new -key <name>.key -out <name>.csr -subj "/CN=<name>/O=<groupname>"
```

```sh
openssl x509 -req -in <name>.csr -CA /minikube/ca.crt -CAkey /minikube/ca.key -CAcreateserial -out <name>.crt -days <numberofdays>
```

```sh
kubectl config set-credentials <name> --client-certificate=<name>.crt --client-key=<name>.key
```

```sh
kubectl config set-context <nameofcontext> --cluster=minikube --user=<name> --namespace=default
```

```sh
kubectl config use-context <nameofcontext>
```

```sh
kubectl config get-contexts # View the current user
```

---

# Kubeadm

## Initialise the control plane

```sh
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=192.168.56.10
```

```sh
mkdir -p $HOME/.kube
```

```sh
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

```sh
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

```sh
kubeadm token create --print-join-command # Retrieve join command
```

---

## Upgrade kubeadm version

```sh
kubeadm upgrade plan
```

```sh
kubeadm upgrade apply v1.19.0 # or <version_number>
```

```sh
kubectl drain kube-control-plane --ignore-daemonsets
```

```sh
# Update kubelet and kubectl depending on your OS
```

```sh
kubectl uncordon kube-control-plane
```

📌 **Remarques :**  
- `kubectl drain` : Vide un nœud de ses workloads (sauf les daemonsets).  
- `kubectl uncordon` : Restaure la capacité du nœud à accepter de nouveaux pods.

---

## Upgrade worker node

```sh
sudo kubeadm upgrade node
```

```sh
kubectl drain worker --ignore-daemonsets
```

```sh
kubectl uncordon worker
```

---

## Backing Up etcd

```sh
sudo ETCDCTL_API=3 etcdctl \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/etcd-backup.db
```

---

## Restoring etcd

```sh
sudo ETCDCTL_API=3 etcdctl \
--data-dir=/var/lib/from-backup \
snapshot restore /opt/etcd-backup.db
```
