# Practice Test - Services
  - Take me to [Practice Test](https://kodekloud.com/courses/539883/lectures/9816583)

#### Solutions to Practice test - Services

- Run the command **`kubectl get services`** and count the number of services.
  
  <details>

  ```
  $ kubectl get services
  ```
  </details>

- Run the command **`kubectl get services`** and look at the Type column

  <details>

  ```
  $ kubectl get services
  ```
  </details>

- Run the command **`kubectl describe service`** and look at TargetPort.

  <details>

  ```
  $ kubectl describe service|grep TargetPort
  ```
  </details>

- Run the command **`kubectl describe service`** and look at Labels

  <details>

  ```
  $ kubectl describe service
  ```
  </details>

- Run the command **`kubectl describe service`** and look at Endpoints
  
  <details>

  ```
  $ kubectl describe service
  ```
  </details>

- Run the command **`kubectl get deployment`** and count the number of services - 1.

  <details>

  ```
  $ kubectl get deployment
  ```
  </details>

- Run the command **`kubectl describe deployment`** and look name of image under the containers section .

  <details>

  ```
  $ kubectl describe deployment
  ```
  </details>

- Try to access the Web Application UI using the tab simple-webapp-ui above the terminal.

- Update the given values in the service definition file and create the service.

  <details>

  ```
  $ kubectl create -f service-definition-1.yaml
  ```
  
  ```
  root@controlplane:~# cat service-definition-1.yaml 
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 8080
      port: 8080
      nodePort: 30080
  selector:
    name: simple-webapp
  ```
  
  ```
  root@controlplane:~# kubectl get services
  NAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
  kubernetes       ClusterIP   10.96.0.1      <none>        443/TCP          21m
  webapp-service   NodePort    10.110.67.56   <none>        8080:30080/TCP   3s
  ```
  
  </details>


#### Take me to [Practice Test - Solutions](https://kodekloud.com/courses/539883/lectures/16603611)








 
