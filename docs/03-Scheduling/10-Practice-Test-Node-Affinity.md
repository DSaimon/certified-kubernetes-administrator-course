# Practice Test - Node Affinity
  - Take me to [Practice Test](https://kodekloud.com/courses/539883/lectures/10277999)
  
Solutions to practice test - node affinity

- Run the command 'kubectl describe node node01' and count the number of labels under **`Labels Section`**.
  
  <details>

  ```
  $ kubectl describe node node01
  ```
  
  ```
  root@controlplane:~# kubectl describe nodes node01
  Name:               node01
  Roles:              <none>
  Labels:             beta.kubernetes.io/arch=amd64
                      beta.kubernetes.io/os=linux
                      kubernetes.io/arch=amd64
                      kubernetes.io/hostname=node01
                      kubernetes.io/os=linux
  ```
  </details>

- Run the command 'kubectl describe node node01' and see the label section
  
  <details>

  ```
  $ kubectl describe node node01
  ```
  </details>

- Run the command 'kubectl label node node01 color=blue'.

  <details>

  ```
  $ kubectl label node node01 color=blue
  ```
  </details>

- Run the below commands

  <details>
 
  OLD DESICION 
    
  ```
  $ kubectl create deployment blue --image=nginx
  $ kubectl scale deployment blue --replicas=3
  ```
  
  NEW DESICION
  
  ```
  root@controlplane:~# kubectl create deployment blue --image=nginx --dry-run -o yaml > deployment-blue.yaml
  W0606 19:15:43.784818   26912 helpers.go:553] --dry-run is deprecated and can be replaced with --dry-run=client.
  ```
  
  ```
  
  ```
    
  </details>
  
  
- Check if master and node01 have any taints on them that will prevent the pods to be scheduled on them. If there are no taints, the pods can be scheduled on either node.
  
  <details>

  ```
  $ kubectl describe nodes|grep -i taints
  $ kubectl get pods -o wide
  ```
  </details>

- Answer file at /var/answers/blue-deployment.yaml
  
  <details>

  ```
  $ kubectl edit deployment blue
  ```
  
  А лучше сделать так - точно верное решение 
    
  ```
  root@controlplane:~# kubectl create deployment blue --image=nginx --dry-run -o yaml > deployment-blue.yaml
  W0606 19:15:43.784818   26912 helpers.go:553] --dry-run is deprecated and can be replaced with --dry-run=client.
  ```
    
  ```
  root@controlplane:~# cat deployment-blue.yaml 
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    creationTimestamp: null
    labels:
      app: blue
    name: blue
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: blue
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: blue
      spec:
        containers:
        - image: nginx
          name: nginx
          resources: {}     
        affinity:
         nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
              nodeSelectorTerms:
              - matchExpressions:
                - key: color
                  operator: In
                  values:
                  - blue
  status: {}
  ```
    
  </details>

  
  Add the below under the template.spec section
  
  <details>

  ```
  affinity:
      nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: color
                operator: In
                values:
                - blue
   ```
   </details>

 - Run the command 'kubectl get pods -o wide' and see the Node column
   
   <details>

   ```
   $ kubectl get pods -o wide
   
   root@controlplane:~# kubectl get pods -o wide
   NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
   blue-566c768bd6-4c7f4   1/1     Running   0          27m     10.244.1.6   node01         <none>           <none>
   blue-566c768bd6-zp979   1/1     Running   0          28m     10.244.1.5   node01         <none>           <none>
   ```
   </details>

 - Answer file at /var/answers/red-deployment.yaml
   Add the below under the template.spec section
   
   <details>

   ```
   affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
   ```
   
   ```
   root@controlplane:~# cat deployment-red.yaml 
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     creationTimestamp: null
     labels:
     app: red
     name: red
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: red
     strategy: {}
     template:
       metadata:
         creationTimestamp: null
         labels:
           app: red
       spec:
         containers:
         - image: nginx
           name: nginx
           resources: {}
         affinity:
          nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
              nodeSelectorTerms:
              - matchExpressions:
                - key: node-role.kubernetes.io/master
                  operator: Exists
   status: {}
   ```
     
   ```
   $ kubectl create -f red-deployment.yaml
   ```
   ```
   $ kubectl get pods -o wide
     
   root@controlplane:~# kubectl get pods -o wide
   NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
   red-5cbd45ccb6-chvs9    1/1     Running   0          3m56s   10.244.0.4   controlplane   <none>           <none>
   red-5cbd45ccb6-dmjsw    1/1     Running   0          3m56s   10.244.0.5   controlplane   <none>           <none>
   ```
   </details>
   
  
  
