# Practice Test - DaemonSets
  - Take me to [Practice Test](https://kodekloud.com/courses/539883/lectures/9816595)
  
Solutions to practice test daemonsets
- Run the command kubectl get daemonsets --all-namespaces
  
  <details>

  ```
  $ kubectl get daemonsets --all-namespaces
  ```
  </details>

- Run the command kubectl get daemonsets --all-namespaces

  <details>

  ```
  $ kubectl get daemonsets --all-namespaces
  ```
  </details>

- Run the command kubectl get all --all-namespaces and identify the types

  <details>

  ```
  $ kubectl get all --all-namespaces
  ```
  </details>

  Q4. On how many nodes are the pods scheduled by the DaemonSet kube-proxy
  
- Run the command kubectl describe daemonset kube-proxy --namespace=kube-system

  <details>

  ```
  $ kubectl describe daemonset kube-proxy --namespace=kube-system
  ```
  </details>

  Q5. What is the image used by the POD deployed by the kube-flannel-ds DaemonSet?
  
- Run the command kubectl describe daemonset kube-flannel-ds --namespace=kube-system

  <details>

  ```
  $ kubectl describe daemonset kube-flannel-ds --namespace=kube-system
  ```
  
  ```
  Image:      quay.io/coreos/flannel:v0.13.1-rc1
  ```
  
  </details>
    
  Q6. Deploy a DaemonSet for FluentD Logging.
  
  Use the given specifications.
  
- Create a daemonset

  <details>

  ```
  $ vi ds.yaml
  ```

  ```
  apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    name: elasticsearch
    namespace: kube-system
    labels:
      k8s-app: fluentd-logging
  spec:
    selector:
      matchLabels:
        name: elasticsearch
    template:
      metadata:
        labels:
          name: elasticsearch
      spec:
        tolerations:
        # this toleration is to have the daemonset runnable on master nodes
        # remove it if your masters can't run pods
        - key: node-role.kubernetes.io/master
          effect: NoSchedule
        containers:
        - name: elasticsearch
          image: k8s.gcr.io/fluentd-elasticsearch:1.20
  ```
  </details>

- To create the daemonset and list the daemonsets and pods

  <details>

  ```
  $ kubectl create -f ds.yaml
  $ kubectl get ds -n kube-system
  $ kubectl get pod -n kube-system|grep elasticsearch
  ```
  
  Anwwer
  
  ```
  root@controlplane:~# kubectl apply -f ds.yaml
  daemonset.apps/elasticsearch created
  root@controlplane:~# kubectl get ds -n kube-system
  NAME              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
  elasticsearch     1         1         0       1            0           <none>                   17s
  kube-flannel-ds   1         1         1       1            1           <none>                   21m
  kube-proxy        1         1         1       1            1           kubernetes.io/os=linux   21m
  root@controlplane:~# kubectl get pod -n kube-system|grep elasticsearch
  elasticsearch-w9pff                    0/1     ContainerCreating   0          37s
  ```
  
  </details>

#### Take me to [Practice Test - Solutions](https://kodekloud.com/courses/certified-kubernetes-administrator-with-practice-tests/lectures/16603698)
