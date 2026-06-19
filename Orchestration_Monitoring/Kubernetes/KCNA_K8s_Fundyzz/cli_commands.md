Blog post includes covering Kubernetes Fundamental’s in preparation for the KCNA.

# 1. Init-Containers
- cat
- kubectl apply -f countdown-pod.yaml
- kubectl get pods -o wide
- until kubectl logs pod/countdown-pod -c init-countdown --follow --pod-running-timeout=5m; do sleep 1; done; until kubectl logs pod/countdown-pod -c main-container --follow --pod-running-timeout=5m; do sleep 1; done
- kubectl get pods -o wide
# 2. Pods
- kubectl run nginx --image=nginx
- kubectl get pods
- kubectl logs pod/nginx
- kubectl get pods -o wide
- NGINX_IP=$(kubectl get pods -o wide | awk '/nginx/ { print $6 }'); echo $NGINX_IP
- ping -c 3 $NGINX_IP
- ssh worker-1 ping -c 3 $NGINX_IP
- ssh worker-2 ping -c 3 $NGINX_IP
- echo $NGINX_IP
- kubectl run -it --rm curl --image=curlimages/curl:8.4.0 --restart=Never -- http://$NGINX_IP
- kubectl exec -it ubuntu -- bash
- apt update && apt install -y curl
- kubectl run nginx --image=nginx --dry-run=client -o yaml | tee nginx.yaml
- cat <<EOF > combined.yaml
- MYPOD_IP=$(kubectl get pods -o wide | awk '/mypod/ { print $6 }'); echo $MYPOD_IP
- kubectl logs pod/mypod -c sidecar
- kubectl delete pod/mypod --now

# 3. Namespaces
- kubectl get ns
- kubectl -n thissuxns run nginx --image=nginx
- kubectl get pods -o wide
- kubectl -n thissuxns get pods
- kubectl config view
- kubectl config set-context --current --namespace=thissuxns
- kubectl get pods -o wide
- kubectl config set-context --current --namespace=default
- kubectl get pods -o wide
# 4. Labels
- kubectl run nginx --image nginx --port 80 -o yaml --dry-run=client
- kubectl run nginx --image nginx --port 80
- kubectl expose pod/nginx --dry-run=client -o yaml
- kubectl expose pod/nginx
- cat <<EOF > coloured_pods.yaml
- kubectl apply -f coloured_pods.yaml
- kubectl get pods -o wide
- kubectl get all --selector colour=green