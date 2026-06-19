# 1. Start up the Bat Mobile (Minikube)
- Minikube start
- sudo chown -R
a. Change directory owner
- .kube
- .minikube
b. Minikube config set
- Update the version
c. Sudo apt install -y docker.io
- Get docker
d. Kubectl apply -f
e. Kubectl get
- po
- pv
- pvc

# 2. Create Object in YAML files to Confirm Cluster is up
- Kubectl apply -f
- Kubectl get po/pv/pvc