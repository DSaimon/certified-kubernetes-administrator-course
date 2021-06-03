# Practice Test - Namespaces
  - Take me to [Practice Test](https://kodekloud.com/courses/539883/lectures/9816576)

Solutions to practice test for namespaces

1. Run the command **`kubectl get namespace`** and count the number of pods.
   
   <details>

   ```
   $ kubectl get namespace
   ```
   </details>

1. Run the command **`kubectl get pods --namespace=research`**
   
   <details>

   ```
   $ kubectl get pods --namespace=research
   ```
   </details>

1. Run the below command

   <details>

   ```
   $ kubectl run redis --image=redis --namespace=finance
   ```
   </details>

1. Run the command **`kubectl get pods --all-namespaces`**

   <details>

   ```
   $ kubectl get pods --all-namespaces
   ```
   </details>

1. Connectivity Test
   
   Получилось в UI законнектится к db-service порту 3306
   
   так как есть сервис в namespace blue
   
  ```
   root@controlplane:~# kubectl get all --namespace marketing 
   NAME           READY   STATUS    RESTARTS   AGE
   pod/blue       1/1     Running   0          8m11s
   pod/mysql-db   1/1     Running   0          8m11s

   NAME                   TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
   service/blue-service   NodePort   10.101.77.149   <none>        8080:30082/TCP   8m10s
   service/db-service     NodePort   10.108.9.144    <none>        3306:32404/TCP   8m9s
  ```
   
   <details>

   ```
   Host Name: db-service and Host Port: 3306
   ```
   </details>

1. Connectivity Test

   <details>

   ```
   Host Name: db-service.dev.svc.cluster.local and Host Port: 3306
   ```
   </details>


#### Take me to [Practice Test Solutions](https://kodekloud.com/courses/539883/lectures/16416900)
