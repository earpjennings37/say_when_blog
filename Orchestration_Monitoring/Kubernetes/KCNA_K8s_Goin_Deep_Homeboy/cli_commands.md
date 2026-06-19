# 1. API
- kubectl proxy & echo $! > /var/run/kubectl-proxy.pid
- kubectl get pods
- kill $(cat /var/run/kubectl-proxy.pid)
rm /var/run/kubectl-proxy.pid
# 2. Scheduling & Node Name
- kubectl run nginx --image=nginx -o yaml --dry-run=client | tee nginx_scheduler.yaml
- cat <<EOF > nginx_scheduler.yaml
- apt update && apt install -y git jq
- git clone https://github.com/spurin/simple-kubernetes-scheduler-example.git
- cd simple-kubernetes-scheduler-example; more my-scheduler.sh
- ./my_scheduler.sh
- cat <<EOF > nginx_scheduler.yaml
# 3. Storage
- kubectl run --image=ubuntu ubuntu -o yaml --dry-run=client --command sleep infinity | tee ubuntu_emptydir.yaml
- cat <<EOF > ubuntu_emptydir.yaml
- kubectl get pods -o wide
- kubectl exec -it ubuntu -- bash
# 4. Network Policies
- kubectl run nginx --image=nginx
- kubectl expose pod/nginx --port=80
- kubectl run --rm -i --tty curl --image=curlimages/curl:8.4.0 --restart=Never -- sh
- curl nginx.default.svc.cluster.local
- cat <<EOF > networkpolicy.yaml
# 5. Pod Disruption Budgets
- kubectl create deployment nginx --image=nginx --replicas=5
- kubectl cordon control-plane && kubectl delete pods -l app=nginx --field-selector=spec.nodeName=control-plane --now
- kubectl drain worker-2 --delete-emptydir-data=true --ignore-daemonsets=true
- kubectl uncordon control-plane worker-1 worker-2
- kubectl create pdb nginx --selector=app=nginx --min-available=2
- kubectl cordon control-plane worker-1 worker-2; kubectl drain control-plane worker-1 worker-2 --delete-emptydir-data=true --ignore-daemonsets=true
- kubectl uncordon worker-1 worker-2