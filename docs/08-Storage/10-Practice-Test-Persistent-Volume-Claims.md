# Practice Test Persistent Volume Claims

  Lets do the [Labs](https://kodekloud.com/courses/certified-kubernetes-administrator-with-practice-tests/lectures/9816679)

#### Solution

  1. Check the Solution

     <details>

      ```
      kubectl get pods
      ```
    
     </details>

  2. Check the Solution

     <details>

      ```
      kubectl exec webapp -- cat /log/app.log
      ```
     </details>
 
  3. Check the Solution

     <details>

      ```
      No
      ```
     </details>

  4. TASK - VOLUME. Check the Solution
    
     <details>
      
      ```
      kubectl get pod webapp -o yaml > pod.yaml
      vim pod.yaml 
      
      kubectl delete pod webapp --force
      kubectl create -f pod.yaml
      kubectl describe pod webapp
      ```
      
      We see this
  
      ```
      kubectl describe pod webapp
      Containers:
        event-simulator:
        Image:          kodekloud/event-simulator
        Mounts:
          /log from log-volume (rw)
       
      Volumes:
        log-volume:
          Type:          HostPath (bare host directory volume)
          Path:          /var/log/webapp
          HostPathType:  
      ```
   
      Check pod.yaml
   
      ```
      apiVersion: v1
      kind: Pod
      metadata:
        name: webapp
      spec:
        containers:
        - name: event-simulator
          image: kodekloud/event-simulator
          env:
          - name: LOG_HANDLERS
            value: file
          volumeMounts:
          - mountPath: /log
            name: log-volume
      
        volumes:
        - name: log-volume
          hostPath:
            # directory location on host
            path: /var/log/webapp
            # this field is optional
            type: Directory
      ```
      </details>

  5. TASK - PERSISTENT VOLUME. Check the Solution

     <details>
      
      ```
      vim pv.yaml
      
      kubectl create -f pv.yaml
      persistentvolume/pv-log created
  
      root@controlplane:~# kubectl get pv
      NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
      pv-log   100Mi      RWX            Retain           Available                                   9s
      ```
         
      Check  pv.yaml
     
      ```
      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: pv-log
      spec:
        accessModes:
          - ReadWriteMany
        capacity:
          storage: 100Mi
        hostPath:
          path: /pv/log
      ```

     </details>

  6. TASK - PERSISTENT VOLUME CLAIM. Check the Solution

     <details>
      
      ```
      vim pvc.yaml
   
      root@controlplane:~# kubectl create -f pvc.yaml 
      persistentvolumeclaim/claim-log-1 created
  
      root@controlplane:~# kubectl get pvc
      NAME          STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
      claim-log-1   Pending                                                     10s
      ```
  
      ```
      kind: PersistentVolumeClaim
      apiVersion: v1
      metadata:
        name: claim-log-1
      spec:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 50Mi
      ```
     </details>

  7. Check the Solution

     <details>

      ```
      PENDING
      ```
     </details>

  8. Check the Solution

     <details>

      ```
      AVAILABLE
      ```
     </details>

  9. Check the Solution

     <details>

      ```
      Access Modes Mismatch
      ```
     </details>

  10. Check the Solution

      <details>
        
       ```
       vim pvc.yaml
  
       kubectl delete pvc claim-log-1 
       kubectl create -f pvc.yaml
       ```
  
       ```
       kind: PersistentVolumeClaim
       apiVersion: v1
       metadata:
         name: claim-log-1
       spec:
         accessModes:
           - ReadWriteMany
         resources:
           requests:
             storage: 50Mi
       ```
      </details>

  11. Check the Solution

      <details>
  
       ```
       root@controlplane:~# kubectl get pvc
       NAME          STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
       claim-log-1   Bound    pv-log   100Mi      RWX                           9s
       ```
 
       ```
       100Mi
       ```
      </details>

  12. Check the Solution

      <details>
      
       ```
       vim pod.yaml 
  
       kubectl delete pod webapp --force
  
       kubectl create -f pod.yaml
  
       kubectl describe pod webapp
       Containers:
          event-simulator:
            Image:          kodekloud/event-simulator
            Mounts:
              /log from log-volume (rw)
       Volumes:
         log-volume:
           Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
           ClaimName:  claim-log-1
       ```
     
       Check pod.yaml
 
       ```
       apiVersion: v1
       kind: Pod
       metadata:
         name: webapp
       spec:
         containers:
         - name: event-simulator
           image: kodekloud/event-simulator
           env:
           - name: LOG_HANDLERS
             value: file
           volumeMounts:
           - mountPath: /log
             name: log-volume
       
         volumes:
         - name: log-volume
           persistentVolumeClaim:
             claimName: claim-log-1
       ```
      </details>

  13. Check the Solution

      <details>
  
       ```
       kubectl describe pv pv-log
       ```
 
       ```
       Retain
       ```
      </details>

  14. TASK. Check the Solution
      
      ```
      What would happen to PV if PVC was destroyed 
      ```

      <details>
       
 
       ```
       The PV is not delete but not available
       ```
      </details>

  15. Check the Solution

      <details>
    
       ```
       kubectl delete pvc claim-log-1 
       ```
 
       ```
       The PVC is stuck in `terminating` state
       ```
      </details>

  16. Check the Solution

      <details>
 
       ```
       The PVC is being used by a POD
       ```
      </details>

  17. Check the Solution

      <details>
 
       ```
       kubectl delete pod webapp --force
         
       kubectl get pod, pvc
       ```
      </details>

  18. Check the Solution

      <details>
 
       ```
       Deleted
       ```
      </details>

  19. Check the Solution

      <details>
 
       ```
       Released
       ```
      </details>
