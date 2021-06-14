# Practice Test - Node Affinity
  - Take me to [Practice Test](https://kodekloud.com/courses/539883/lectures/10277999)
  
Solutions to practice test - node affinity

Q1. How many Labels exist on node node01 ?

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
  
  Лучше даже использовать команду
  
  ```
  kubectl get nodes node01 --show-labels
  ```
  
  ```
  Ответ: 5
  ```
  
  </details>

Q2. What is the value set to the label beta.kubernetes.io/arch on node01?
  
- Run the command 'kubectl describe node node01' and see the label section
  
  <details>

  ```
  $ kubectl describe node node01
  ```
  
  Лучше даже использовать команду
  
  ```
  kubectl get nodes node01 --show-labels
  ```
  
  ```
  Answer. beta.kubernetes.io/arch=amd64 
  ```
  
  </details>

Q3. Apply a label color=blue to node node01
  
- Run the command 'kubectl label node node01 color=blue'.

  <details>

  ```
  $ kubectl label node node01 color=blue
  ```
  
  Answer
    
  ```  
  root@controlplane:~# kubectl label node node01 color=blue
  node/node01 labeled
  ```
    
  </details>

Q4. Create a new deployment named blue with the nginx image and 3 replicas
  
- Run the below commands

  <details>
 
  OLD DESICION 
    
  ```
  $ kubectl create deployment blue --image=nginx
  $ kubectl scale deployment blue --replicas=3
  ```
  
  ```
  root@controlplane:~#  kubectl create deployment blue --image=nginx
  deployment.apps/blue created
  root@controlplane:~# kubectl scale deployment blue --replicas=3
  deployment.apps/blue scaled
  ```
    
  NEW DESICION
  
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
    replicas: 1
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
  status: {}
  ```
  
  ```
  root@controlplane:~#vim deployment-blue.yaml
  spec:
    replicas: 3
  ```
    
  ```
  root@controlplane:~# kubectl create -f deployment-blue.yaml 
  deployment.apps/blue created
  ```
    
  Проверка
    
  ```
  root@controlplane:~# kubectl get all
  NAME                        READY   STATUS    RESTARTS   AGE
  pod/blue-7bb46df96d-9jt45   1/1     Running   0          114s
  pod/blue-7bb46df96d-pnlbf   1/1     Running   0          114s
  pod/blue-7bb46df96d-tkn5g   1/1     Running   0          114s
  
  NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
  service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   22m
  
  NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
  deployment.apps/blue   3/3     3            3           115s
  
  NAME                              DESIRED   CURRENT   READY   AGE
  replicaset.apps/blue-7bb46df96d   3         3         3       114s
  ```
    
  ```
  root@controlplane:~# kubectl get deployments.apps blue 
  NAME   READY   UP-TO-DATE   AVAILABLE   AGE
  blue   3/3     3            3           8m2s
  ```
  </details>
  
Q5. Which nodes can the pods for the blue deployment placed on? Make sure to check taints on both nodes!
    
- Check if master and node01 have any taints on them that will prevent the pods to be scheduled on them. If there are no taints, the pods can be scheduled on either node.
  
  <details>

  ```
  $ kubectl describe nodes|grep -i taints
  $ kubectl get pods -o wide
  ```
  
  Вывод команд
    
  ```
  root@controlplane:~# kubectl describe nodes|grep -i taints
  Taints:             <none>
  Taints:             <none>
  ```
  
  ```
  root@controlplane:~# kubectl get pods -o wide
  NAME                    READY   STATUS    RESTARTS   AGE   IP           NODE     NOMINATED NODE   READINESS GATES
  blue-7bb46df96d-9jt45   1/1     Running   0          11m   10.244.1.3   node01   <none>           <none>
  blue-7bb46df96d-pnlbf   1/1     Running   0          11m   10.244.1.2   node01   <none>           <none>
  blue-7bb46df96d-tkn5g   1/1     Running   0          11m   10.244.1.4   node01   <none>           <none>
  ```
    
  Answer. 
    
  ```
  master/controlplane and node01
  ```
    
  </details>

Q6. Set Node Affinity to the deployment to place the pods on node01 only
  
  Нет такого ответа уже в лабе. Остатки старых вариантов лаб.
  
- Answer file at /var/answers/blue-deployment.yaml
  
  <details>
  
  Вариант 1. 
  Можно попробовать таким способом, но не пробовал с ним. Главный вопрос - в том что похоже при таком внесении измений - они тупо не сохранятся и при рестарте не получим      требуемый результат ответ
    
  ```
  $ kubectl edit deployment blue
  ```
  
  Вариант 2. Как в видео1
    
  ```
  root@controlplane:~# kubectl get deployments.apps blue -o yaml > blue.yaml
  ```
    
  Далее редактируем здоровенный файл - vim blue.yaml
  
  Далее смотрим по ссылке
   
  https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/
    
  и видим что нужно добавлять в разделе 
    
  Schedule a Pod using required node affinity
  
  кусочек
    
  ```
  spec:
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: disktype
              operator: In
              values:
              - ssd            
  ```
  
  добавляем, единственное меняем key: color и values: - blue
    
  И далее удаляем старый deployment и поднимает новвый
    
  ```
  root@controlplane:~# kubectl delete deployments.apps blue
  deployment.apps "blue" deleted
  ```
  
  ```
  root@controlplane:~# kubectl apply -f blue.yaml
  deployment.apps/blue created
  ```
    
  И Бинго - ответ верный
    
  Вариант 3. Если у нас совсем нет deployment-blue.yaml
    
  то создаем его такой командой
    
  ```
  root@controlplane:~# kubectl create deployment blue --image=nginx --dry-run -o yaml > deployment-blue.yaml
  
  видим такое предупреждение
  W0606 19:15:43.784818   26912 helpers.go:553] --dry-run is deprecated and can be replaced with --dry-run=client.
  ```
    
  И фигачим такой файл с добавлением - nodeAffinity
    
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
   
  Получается добавили следующуюю часть 
  
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
  
  
  Удаляем старый деплоймент и применяем нужный нам файл Deployment
 
  ```
  root@controlplane:~# kubectl delete deployments.apps blue
  deployment.apps "blue" deleted
  ```
  
  ```
  root@controlplane:~# kubectl apply -f blue.yaml
  deployment.apps/blue created
  ```
  
  Бинго. Все работает
   
  </details>

  
Q7. Which nodes are the pods placed on now?
  
 - Run the command 'kubectl get pods -o wide' and see the Node column
   
   <details>
   
   ```
   root@controlplane:~#  kubectl get pods -o wide
   NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE     NOMINATED NODE   READINESS GATES
   blue-566c768bd6-5m66x   1/1     Running   0          9m18s   10.244.1.5   node01   <none>           <none>
   blue-566c768bd6-78v9c   1/1     Running   0          9m17s   10.244.1.7   node01   <none>           <none>
   blue-566c768bd6-x744r   1/1     Running   0          9m17s   10.244.1.6   node01   <none>           <none>
   ```
     
   Answer. node01
    
    
   </details>
 
 Q8. Create a new deployment named red with the nginx image and 2 replicas, and ensure it gets placed on the master/controlplane node only.
     
 Use the label - node-role.kubernetes.io/master - set on the master/controlplane node.
     
   Name: red
   Replicas: 2
   Image: nginx
   NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution
   Key: node-role.kubernetes.io/master
   Use the right operator
     
   
     
 - Answer file at /var/answers/red-deployment.yaml
   Add the below under the template.spec section
   
   <details>
    
   Полный ход решения следующий:
     
   ```
   root@controlplane:~# kubectl create deployment red --image=nginx --dry-run -o yaml > deployment-red.yaml
   W0606 20:19:25.578516   20389 helpers.go:553] --dry-run is deprecated and can be replaced with --dry-run=client.    
   ```
   
   По сути нам надо добавить строки - для верного решения укажи node - master, c controlplane - ошибка, хотя k get pods -o wide - покажет, что поды на ноде control
     
   ```
   affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
   ```
   
   и не забыть увеличить кол-во Replica до 2.
     
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
   root@controlplane:~# kubectl create -f deployment-red.yaml 
   deployment.apps/red created
   ```
     
   либо команда
     
   ```
   root@controlplane:~# kubectl apply -f deployment-red.yaml 
   deployment.apps/red created
   ```
   ```
   $ kubectl get pods -o wide
   ```
     
   ```
   root@controlplane:~# kubectl get pods -o wide
   NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
   blue-566c768bd6-5m66x   1/1     Running   0          31m     10.244.1.5   node01         <none>           <none>
   blue-566c768bd6-78v9c   1/1     Running   0          31m     10.244.1.7   node01         <none>           <none>
   blue-566c768bd6-x744r   1/1     Running   0          31m     10.244.1.6   node01         <none>           <none>
   red-5cbd45ccb6-9wjhq    1/1     Running   0          4m12s   10.244.0.6   controlplane   <none>           <none>
   red-5cbd45ccb6-blslb    1/1     Running   0          4m12s   10.244.0.5   controlplane   <none>           <none>
   ```
   
    
   </details>
   
  
  
