# Abstract 
This is a cheat sheet for deploying applications using *OpenShift*. 

**OpenShift** is a containerization software developed by RedHat. OpenShift container plateform is a PasS 
  build around docker containers, managed and orchestrated by Kubernetes. 
  
  
  > The used version for this is *oc v3.7.72* \
  > Here' s the link to the [OpenShift official Documentation](https://docs.openshift.com/container-platform/3.7/dev_guide/index.html) \
  > Here's the link to [Kubernetes official Documentation](https://kubernetes.io/docs/concepts/)
  
  ## 1. First hand with pods 
  ### Stating with OpenShift
  Login to plateform  
  ```
  oc login (link_to_plateform)
  ``` 
  Get version  
  ```
  oc version
  ``` 
  Get username 
  ```
  oc whoami
  ``` 
  Switching between projects  
  ```
  oc project (name_of_the_project)
  ```
 
  
  ### Deploying pods 
  Creating pod 
  ```
  oc run (nameOfPod) --image (pathToContainerizedApp) --restart (restartOption=Never) --labels (labels="lab=pod")
  ``` 
  Creating pod using file 
  ```
  oc apply -f (fileName.yaml)
  ``` 
  
  
  
  ### Monitoring pods
   Get all running OR installed ressources 
  ```
  oc get all
  ``` 
  Checking status  
  ```
  watch oc get pod (name)
  ```  
  Get where pod is running  
  ```
  oc get pod (nameOfPod) -o wide
  ``` 
  Getting pod configuration after deployment 
  ```
  oc get pod (name) -o (format=yaml)
  ```
  
  Checking events 
  ```
  oc describe pod (nameOfPod)
  ``` 
  Checking logs  
  ```
  oc logs (nameOfPod)
  ``` 
  
  Getting command as a yaml file 
  ```
  oc run (name) --image (path) -- restart (value) --labels ("lab=val") --dry-run -o (format=yaml)
  ```
  
  
  ### Deleting pods
  Delete one pod
  ```
  oc delete pod (name)
  ``` 
  Delete all with label 
  ```
  oc delete all -l (label=value)
  ```
   Deleting everything in namespace 
  ```
  oc delete --all all
  ```
  
  ## 2. Replication Controller 
  Running pods with replicas
  ```
  oc run (name) --image (path) --replicas (number) --generator (?=run/v1) --labels ("lab=val") --save-config
  ```
  Get rc events 
  ```
  oc describe rc (name)
  ```
  Get logs of pod with replication controller 
  ```
  oc logs $(oc get pod -l (lab=rc) -o name | tail -1)
  ```
  
  ## 3. Deployment Configs
  Trigger re-deployment 
  ```
  oc rollout latest (name)
  ```
  Go back to previous deployment configuration 
  ```
  oc rollout undo (dc/name) [--to-revision=(number)]
  ```
  Get general status 
  ```
  oc status
  ``` 
  
  ## 4. Services
  Expose the app with port 
  ```
  oc run (name) --image (path) --replicas (number) --port (number) --expose --labels (lab=val) --save-config
  ```
  Get events of service
  ```
  oc describe svc (name) 
  ```
  Get Endpoints of exposed app 
  ```
  oc get endpoints -l (lab=svc)
  ```
  Scaling up servers 
  ```
  oc scale (dc/name) --replicas (number)
  ```
  
  ## 5. Environment customization
  The goal is to be able to promote the application across various environments.
  > Application promotion means moving the app through different runtime environements (Development -> Stage -> Production). 
  > It requires customizing the application accordingly.
  > - **Staticly** : at startup by setting environment variable on the pod config. 
  > - **Dynamically** : contiously reading and updating app behaviour, by defining the configuration in a separate object (from the pod) called the ConfigMap. 
  
  ### Configuring app
  Get configMaps objects
  ```
  oc get cm
  ```
  Checking deployment configuration as yaml
  ```
  oc export dc (name)
  ```
  
  Setting environement variable 
  ```
  oc set env dc (name) -e VAR=value
  ```
  Instantiante template with parameters file and environement variables
  ```
  oc process -f (templateFile) --param-file (parametersFile) (VAR=val) | oc apply -f -
  ```
  
  ### Using ConfigMaps objects
  Create a ConfigMap 
  ```
  oc create configmap (name:params) --from-literal (VAR=val:PERIOD=2)
  ```
  Configure the app to read the ConfigMap
  ```
  oc set env (dc/name) --from=(configmap/name:configmap/params)
  ``` 
  
  ### Using Secrets
  Create a secret 
  ```
  oc create secret generic (name:user) --from-literal (VAR=val:WHO="Hidden User")
  ```
  Configure app to read secret 
  ```
  oc set env (dc/name:dc/dateloop) --from (secret/name:secret/user)
  ```
  
  ### Dynamic configuration 
  Create ConfigMap with file
  ```
  oc apply -f configmaps/nameDynamicConfigMapFile
  ``` 
  Deploy using ConfigMap 
  ```
  oc apply -f dynamicTemplateAppFile
  ```
  
  ## 6. Application health checks
  The goal is to detect and handle unhealthy containers. 
  
  Adding a readiness probe to the application
  ```
  oc set probe (dc/name: dc/dateloop) --readiness --period-seconds (number:2) -- test -f (pathToFile://var/run/app/ready)
  ```
  
  Trigger the temporary failure of the app
  ```
  oc exec $(oc get pod -l lab=probe --no-headers | cut -d ' ' -f 1) -- rm //var/run/app/ready
  ```
  
  Adding a liveness probe to the application (kill and restart container to hopefully recover)
  ```
  readinessProbe:
            exec:
              command:
              - test
              - -f
              - /var/run/app/ready
            periodSeconds: 2
          livenessProbe:
            exec:
              command:
              - cat
              - /var/run/app/alive
            periodSeconds: 2
            
  # Simulating  failure
  oc exec $(oc get pod -l lab=probe --no-headers | cut -d ' ' -f 1) -- rm //var/run/app/alive
  ```

  ## 7. Resource quotas and requests
  Checking the ressources quotas
  ```
  oc get AppliedClusterResourceQuota 
  oc describe AppliedClusterResourceQuota
  ```
  
  Specifying CPU & Memory limit
  ```
  spec:
        containers:
        - args:
          resources:
            requests:
              cpu: "200m"
              memory: "4Mi"
            limits:
              cpu: "${CPU_LIMIT}"
              memory: "4Mi" # Use the lowest possible memory limit
  ```
  
  ## 8. Pod autoscaling 
  The autoscaler definition in yaml file
  ```
  - apiVersion: autoscaling/v1
    kind: HorizontalPodAutoscaler
    metadata:
      name: dateloop
    spec:
      scaleTargetRef:
        kind: DeploymentConfig
        name: dateloop
        apiVersion: v1
        subresource: scale
      minReplicas: ${{MIN_REPLICAS}}
      maxReplicas: ${{MAX_REPLICAS}}
      cpuUtilization:
        targetPercentage: ${{CPU_THRESHOLD}}
  ```
     
  Get events of autoscaler (HPA = Horizontal Pod Autoscaler)
  ```
  oc describe hpa/(name:dateloop)
  ```
     
     
