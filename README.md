# Deploying MongoDB and Mongo Express on Minikube

## **Step 1: Apply Kubernetes Configurations**
Execute the following commands **in order** to deploy MongoDB and Mongo Express:

```sh
kubectl apply -f mongo-secret.yaml       # Apply secrets for MongoDB authentication
kubectl apply -f mongo.yaml              # Deploy MongoDB
kubectl apply -f mongo-configmap.yaml    # Apply ConfigMap for MongoDB configuration
kubectl apply -f mongo-express.yaml      # Deploy Mongo Express (Web UI for MongoDB)
```

## **Step 2: Verify Deployments**
After applying the configurations, check the status of the pods and services using the following commands:

### **Check Running Pods**
```sh
kubectl get pod                          # List all running pods
kubectl get pod --watch                  # Continuously watch pod status updates
kubectl get pod -o wide                  # Get detailed pod information including node placement
```

### **Check Services**
```sh
kubectl get service                      # List all running services
```

### **Check Secrets**
```sh
kubectl get secret                       # List all secrets (verify mongo-secret is created)
```

### **Check All Resources Related to MongoDB**
```sh
kubectl get all | findstr mongodb        # Windows users: Filter MongoDB-related resources
kubectl get all | grep mongodb           # Linux/Mac users: Filter MongoDB-related resources
```

## **Step 3: Debugging and Troubleshooting**
If something isn’t working as expected, use these debugging commands:

### **Describe Pods and Services**
```sh
kubectl describe pod <mongodb-pod-name>       # Get detailed pod status and events
kubectl describe service mongodb-service      # Check MongoDB service configuration
```

### **Check Logs**
```sh
kubectl logs <mongo-express-pod-name>         # View logs for Mongo Express to check errors
```

## **Step 4: Access Mongo Express in Minikube**
To get the external URL for Mongo Express:
```sh
minikube service mongo-express --url
```
This will return a URL like:
```
http://127.0.0.1:<PORT>
```
Open this URL in your browser to access Mongo Express.

If the above command doesn’t work, try manually forwarding the port:
```sh
kubectl port-forward service/mongo-express 8081:8081
```
Then access Mongo Express at:
```
http://127.0.0.1:8081
```

## **Step 5: Get Minikube IP (Alternative Method)**
If `127.0.0.1:<PORT>` is not working, check the Minikube IP:
```sh
minikube ip
```
If it returns `192.168.49.2`, then access Mongo Express at:
```
http://192.168.49.2:<PORT>
```

## **Common Issues & Fixes**
### **1. Mongo Shell Not Found Inside the Container**
If you get an error like:
```
exec failed: unable to start container process: exec: "mongo": executable file not found
```
Try using `mongosh` instead:
```sh
kubectl exec -it <mongodb-pod-name> -- mongosh -u admin -p password --authenticationDatabase admin
```
If `mongosh` is also missing, install it inside the container:
```sh
kubectl exec -it <mongodb-pod-name> -- bash
apt update && apt install -y mongodb-clients
mongosh -u admin -p password --authenticationDatabase admin
```

### **2. Minikube Service URL Not Working**
- Try `minikube service list` to see if Mongo Express is running.
- Use `kubectl port-forward service/mongo-express 8081:8081` and open `http://127.0.0.1:8081`.
- Run `minikube ip` and access `http://<Minikube-IP>:<PORT>`.

### **3. Restarting Minikube (Last Resort)**
```sh
minikube stop
minikube delete
minikube start
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo.yaml
kubectl apply -f mongo-configmap.yaml
kubectl apply -f mongo-express.yaml
```

---
This guide ensures a smooth deployment of MongoDB and Mongo Express on Minikube. 🚀
