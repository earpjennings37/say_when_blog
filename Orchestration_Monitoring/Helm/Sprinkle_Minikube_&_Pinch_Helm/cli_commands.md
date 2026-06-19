# 1. Start Minikube Cluster:
- sudo minikube start --vm-driver none
# 2. Tar, mv, & init:
- tar -xvzf ~/helm.tar.gz
- sudo mv ./helm /usr/local/bin
- sudo mv ./tiller /usr/local/bin
- sudo helm init
# 3. Install Namespace w/Helm:
- sudo kubectl create namespace robot-shop
- sudo helm install --name robot-shop --namespace robot-shop .
- sudo kubeclt -n robot-shop get po
# 4. Edit Nodeport & Configure Nginx to Proxy:
- sudo kubeclt -n robot-shop get svc web -o yaml
- sudo kubectl -n robot-shop edit svc web
a. under spec, type: LoadBalancer to NodePort
- sudo kubectl -n robot-shop edit svc web
- sudo kubectl -n robot-shop get svc web
- sudo minikube service list
- sudo systemctl restart nginx