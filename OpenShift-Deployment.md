# Abstract 
This is a cheat sheet for deploying applications using *OpenShift*. 

**OpenShift** is a containerization software developed by RedHat. OpenShift container plateform is a PasS 
  build around docker containers, managed and orchestrated by Kubernetes. 
  
  
  > The used version for this is *oc v3.7.72* \
  > Here' s the link to the [official Documentation](https://docs.openshift.com/container-platform/3.7/dev_guide/index.html)
  
  ## 1. Pods 
  ### Stating with project
  Login to plateform : `oc login (link_to_plateform)` \
  Checking for version : `oc version` \
  Checking for username : `oc whoami` \
  Check for running OR installed ressources : `oc get all` \
  Switching between projects : `oc project (name_of_the_project)` \
  Deleting everything in namespace : `oc delete --all all`
  
  ### Deploying pod
  Creating pod : 
  ```
  oc run (nameOfPod) --image (pathToContainerizedApp) --restart (restartOption=Never) 
  --labels (labels="lab=pod")
  ``` 
  Checking for events of pod : `oc describe pod (nameOfPod)` \
  Check for logs : `oc logs (nameOfPod)` \
  Checking where pod is running : `oc get pod (nameOfPod) -o wide` 
