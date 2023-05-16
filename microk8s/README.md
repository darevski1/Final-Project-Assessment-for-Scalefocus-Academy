### microk8s 

1. Installing microk8s on ubuntu

    sudo snap install microk8s --classic

2. Check the status while Kubernetes starts

    microk8s enable dashboard dns registry istio

3. Turn on the services you want

    microk8s kubectl get all --all-namespaces

4. Access the Kubernetes dashboard
    
    microk8s dashboard-proxy


After the installation of **microk8s** we can create new namespaces called jenkins

    microk8s kubectl create namespace jenkins
namespace/jenkins created





