# Practice Test - Scheduling
  - Take me to [Video Tutorial](https://kodekloud.com/courses/539883/lectures/9816589)

```
root@controlplane:~# kubectl describe pods auth            
Name:         auth
Namespace:    default
Priority:     0
Node:         controlplane/10.61.161.9
Start Time:   Sun, 06 Jun 2021 11:08:50 +0000
Labels:       bu=finance
              env=prod
Annotations:  <none>
```

```
root@controlplane:~# kubectl get pod auth -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2021-06-06T11:08:49Z"
  labels:
    bu: finance
    env: prod
```

```
root@controlplane:~# kubectl describe pods app-1-4b45s
Name:         app-1-4b45s
Namespace:    default
Priority:     0
Node:         controlplane/10.61.161.9
Start Time:   Sun, 06 Jun 2021 11:08:49 +0000
Labels:       bu=finance
              env=dev
              tier=frontend
Annotations:  <none>
```
  
```
root@controlplane:~# kubectl get pod app-1-4b45s -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2021-06-06T11:08:49Z"
  generateName: app-1-
  labels:
    bu: finance
    env: dev
    tier: frontend
```

Solutions to Practice Test - Scheduling
- Run the command 'kubectl get pods --selector env=dev'
  
  <details>

  ```
  $ kubectl get pods --selector env=dev
  ```
  </details>

- Run the command 'kubectl get pods --selector bu=finance'

  <details>

  ```
  $ kubectl get pods --selector bu=finance
  ```
  </details>

- Run the command 'kubectl get all --selector env=prod'

  <details>

  ```
  $ kubectl get all --selector env=prod
  ```
  </details>

- Run the command 'kubectl get all --selector env=prod,bu=finance,tier=frontend'
  
  <details>

  ```
  $ kubectl get all --selector env=prod,bu=finance,tier=frontend
  ```
  </details>

- Set the labels on the pod definition template to frontend

  <details>

  ```
  $ vi replicaset-definition.yaml
  $ kubectl create -f replicaset-definition.yaml
  ```
  </details>

  
#### Take to [Practice Test - Scheduling](https://kodekloud.com/courses/certified-kubernetes-administrator-with-practice-tests/lectures/13290011)
