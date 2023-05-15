# Final-Project-Assessment-for-Scalefocus-Academy

Deploy a WordPress on Kubernetes (using Minicube) with Helm and
automation with Jenkins.

Prerequisites:
1. Install the necessary tools: Minicube, Helm and Jenkins.
2. Separate repo in your GitHub Profile named: Final Project Assessment for Scalefocus Academy
Requirement for the Project Assessment:
1. Download Helm chart for WordPress. ( Bitnami chart:
https://github.com/bitnami/charts/tree/main/bitnami/wordpress )
2. In values.yaml, you need to change line 543 from type: LoadBalancer to type: ClusterIP ( Hint: there
will be one more problem when deploying. Resolve it. )
3. Create a Jenkins pipeline that checks if wp namespace exists, if it doesn’t then it creates one.
Checks if WordPress exists, if it doesn’t then it installs the chart.
4. Name the Helm Deployment as: final-project-wp-scalefocus.
5. Deploy the helm chart using the Jenkins pipeline.
6. Load the home page of the WordPress to see the final result.
7. Explain the project directly in a README.md file in your project repo.


# Installing nesesery tools ??
- minikube
- helm chart 
- docker
- jeniks


Now we are going configure everything we need to deploy Jenkins. We will need the following:

1. Namespace
2. Persisten Volume


Create two yml files

jenkins-namespace.yaml

    apiVersion: v1
    kind: Namespace
    metadata:
        name: jenkins

To create the namespace in your cluster use kubectl

    kubectl create -f jenkins-namespace.yaml

And we can check this by using

    kubectl get ns

Now lets create new persisten volume for this repository create new file **jenkins-volume.yaml**

    apiVersion: v1
    kind: PersistentVolume
    metadata:
    name: jenkins-pv
    namespace: jenkins
    spec:
    storageClassName: jenkins-pv
    accessModes:
        - ReadWriteOnce
    capacity:
        storage: 20Gi
    persistentVolumeReclaimPolicy: Retain
    hostPath:
        path: /data/jenkins-volume/


    kubectl create -f jenkins-volume.yaml


