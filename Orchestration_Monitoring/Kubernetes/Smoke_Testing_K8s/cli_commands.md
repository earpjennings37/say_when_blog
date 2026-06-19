# 1. Cluster Data Encryption
- Create test data for secret key
- Ensure secret key is stored
# 2. Deployments Work
- kubectl run nginx --image=nginx
- kubectl get pods -l run=nginx
- kubectl get pods -l run=nginx
# 3. Remote Access works w/Port Forwarding
- Snag that pod name & store in variable
- Forward port to nginx pod
- Open new terminal – – – & curl IP address/port
# 4. Access Container Logs w/Kubectl Logs
- kubectl logs $POD_NAME
# 5. Execute Commands inside the Container
- kubectl exec -ti $POD_NAME -- nginx -v
- nginx version: nginx/1.19.19
# 6. Services Work
- Test to see if service can be deployed
- Get node port from variable
- Curl IP address/port