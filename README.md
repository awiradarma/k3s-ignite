# Running k3s on Firecracker via Ignite

## Set up a micro-vm and install k3s:
1. import the centos image  
    ```
    ignite image import weaveworks/ignite-centos:7
    ```
2. create a micro-vm using the centos image  
    ```
    ignite create weaveworks/ignite-centos:7 --name k3s --cpus 2 --memory 2GB --size 10GB --ssh
    ```
3. start the newly created micro-vm  
    ```
    ignite start k3s
    ```
4. attach to the tty of the new micro-vm  
    ```
    ignite attach k3s
    ```

5. login into the micro-vm (use *root/root*)


6. install a few required tools  
    ```
    yum install which  
    yum install curl
    ```
7. install k3s
    ```
    curl -sfL https://get.k3s.io | sh -
    ```
8. validate the installation
    ```
    kubectl version
    kubectl get nodes
    kubectl get po --all-namespaces
    ```
9. at this point, you should have a working k3s cluster
  
  
## Deploy a few applications


1. deploy the hello app and validate that the pods are started
    ```
    kubectl apply -f hello.yml -n default
    kubectl get po -n default
    kubectl get svc -n default
    ```
2. create the ingress object  
    ```
    kubectl apply -f ingress_hello.yml -n default
    ```
3. create a world namespace and deploy world there
    ```
    kubectl create ns world
    kubectl apply -f world.yml -n world
    kubectl get po -n world
    ```

7. validate !
    ```
    curl http://localhost/hello
    ```

## Install Weave Scope

1. deploy weave scope
    ```
    kubectl apply -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"
    ```
2. create the ingress object
    ```
    kubectl apply -f ingress_scope.yml -n weave
    ```

4. Hit the URL, replace the IP address with the ignite microvm IP address
    ```
    curl http://172.17.0.2/scope/index.html
    ```

---
**note**: To detach from the TTY of a micro-vm, enter the key combination ^P^Q (Ctrl + P + Q)