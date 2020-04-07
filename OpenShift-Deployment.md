# Abstract 
This is a cheat sheet for deploying applications using *OpenShift*. 

**OpenShift** is a containerization software developed by RedHat. OpenShift container plateform is a PasS 
  build around docker containers, managed and orchestrated by Kubernetes. 
  
  
  > The used version for this is *oc v3.7.72* \
  > Here' s the link to the [official Documentation](https://docs.openshift.com/container-platform/3.7/dev_guide/index.html)
  
  ## 1. First Pods 
  ### Stating with project
  Login to plateform : `oc login (link_to_plateform)` \
  Get version : `oc version` \
  Get username : `oc whoami` \
  Get running OR installed ressources : `oc get all` \
  Switching between projects : `oc project (name_of_the_project)` \
  Deleting everything in namespace : `oc delete --all all`
  
  ### Deploying pod
  Creating pod : 
  ```
  oc run (nameOfPod) --image (pathToContainerizedApp) --restart (restartOption=Never) --labels (labels="lab=pod")
  ``` 
  Creating pod using file : `oc apply -f (fileName.yaml)` \
  Checking events : `oc describe pod (nameOfPod)` \
  Checking logs : `oc logs (nameOfPod)` \
  Checking status : `watch oc get pod (name)` \ 
  Get where pod is running : `oc get pod (nameOfPod) -o wide` \
  
  
  ### Checking configuration
  Checking deployment config : 
  ```
  oc run (name) --image (path) -- restart (value) --labels ("lab=val") --dry-run -o (format=yaml)
  ```
  Checking config after deployment : `oc get pod (name) -o (format=yaml)`
  
  ### Deleting pods
  Delete : `oc delete pod (name)` \ 
  Delete with label : `oc delete all -l (label=value)`
  ## 2. Replication Controller 
  Running pods with replicas : 
  ```
  oc run (name) --image (path) --replicas (number) --generator (?=run/v1) --labels ("lab=val") --save-config
  ```
  Get rc events : `oc describe rc (name)` \
  Access logs : `oc logs $(oc get pod -l (lab=rc) -o name | tail -1)`
  
  ## 3. Deployment Configs
  Trigger redeployment : `oc rollout latest (name)` \
  Go back to previous deployment config : `oc rollout undo (dc/name) [--to-revision=(number)]` \
  Get status : `oc status` 
  
  ## 4. Services
  Expose the app : 
  ```
  oc run (name) --image (path) --replicas (number) --port (number) --expose --labels (lab=val) --save-config
  ```
  Get events : `oc describe svc (name) ` \ 
  Get Endpoints : `oc get endpoints -l (lab=svc)` \
  Scaling up servers : `oc scale (dc/name) --replicas (number)`
  
  ## 5. Environment customization
  The goal is to be able to promote the application across various environments.
  > Application promotion means moving the app through different runtime environements (Development -> Stage -> Production). 
  > It requires customizing the application accordingly. This can be done \
  > **staticly** : at startup by setting environment variable on the pod config. \
  > **dynamically** : contiously reading and updating app behaviour, by defining the configuration in a separate object (from the pod) called the ConfigMap. 
  
  ### Configuring App using templates
  Get configMaps : `oc get cm` \
  Checking deployment configuration :   `oc export dc (name)`
  
  Setting Environement variable : `oc set env dc (name) -e VAR=value` \ 
  Instantiante template : 
  ```
  oc process -f (templateFile) --param-file (parametersFile) (VAR=val) | oc apply -f -
  ```
  
  ### Using ConfigMaps
  Create a ConfigMap : `oc create configmap (name:params) --from-literal (VAR=val:PERIOD=2)` \
  Configure the app to read the ConfigMap : `oc set env (dc/name) --from=(configmap/name:configmap/params)` 
  
  ### Using Secrets
  Create a secret : `oc create secret generic (name:user) --from-literal (VAR=val:WHO="Hidden User")` \
  Configure app to read secret : `oc set env (dc/name:dc/dateloop) --from (secret/name:secret/user)`
  
  
