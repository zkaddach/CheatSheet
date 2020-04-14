# Abstract 
This is a cheat sheet for *Helm* a package manager for Kubernetes deployments. 

**Helm** allows you to: 
-	Create templates of the Kubernetes "yaml files", hence same Chart (Package of your deployment configuration)
  can be used in different environments with different values.
-	Upgrade services when a new Chart version is published
- Manage your application dependencies 

  
  
  > The used version for this is *helm v2.16.5* \
  > Here' s the link to the [Helm official Documentation](https://helm.sh/docs/) 
  
  ## 1. Getting started
  ### Installing  Helm 
  a. Download the package [from github releases](https://github.com/helm/helm/releases)
  b. Extract files and move the binaries to your local environment
  ```
  tar -zxvf pathTo/YourHelmPackage.tar.gz
  cp pathTo/ExtractedFiles/helm /usr/local/bin/helm
  ``` 
  c. Test command with a call for help 
  ```
  helm help 
  ``` 
