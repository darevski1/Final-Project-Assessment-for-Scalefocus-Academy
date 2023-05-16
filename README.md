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

 
To run Jenkins on minikube cluster. We will need the following:
We need to install Jenkins on our minikube cluster and persistent volume we can install with Helm Chart or manifest file. Using manifest files we need to create:

**serviceAccount.yaml**, 
**volume.yaml**, 
**deployment.yaml**, 
**service.yaml'**.


We have to edit the file values.yaml, on line 543 the **LoadBalancer to type: ClusterIP**

After we setup jenkins on minikube we can log in to jenikns and install required plugin **kuberentes** so we can create connection to our local cluster. Next we need to create new pipeline and connect to our respository. Create Jenkinsfile we put our script for deplyoment, the file has 5 stages:

- Validate - stages check is the namespace **wp** exist, if not exist the namespace will be created
- Install wordpress and helm chard, here we install helm binary, and install wordpress helm chart
- Make wordpress availble - in the stage we are running port forwarding,





        Started by user Darko Avramovski
        Obtained Jenkinsfile from git https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git
        [Pipeline] Start of Pipeline
        [Pipeline] node
        Running on Jenkins in /var/jenkins_home/workspace/wp
        [Pipeline] {
        [Pipeline] stage
        [Pipeline] { (Declarative: Checkout SCM)
        [Pipeline] checkout
        Selected Git installation does not exist. Using Default
        The recommended git tool is: NONE
        using credential a428e41a-2029-4ae7-acd2-50eea8d76512
        Cloning the remote Git repository
        Cloning repository https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git
        > git init /var/jenkins_home/workspace/wp # timeout=10
        Fetching upstream changes from https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git
        > git --version # timeout=10
        > git --version # 'git version 2.30.2'
        using GIT_ASKPASS to set credentials 
        > git fetch --tags --force --progress -- https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git +refs/heads/*:refs/remotes/origin/* # timeout=10
        > git config remote.origin.url https://github.com/darevski1/Final-Project-Assessment-for-Scalefocus-Academy.git # timeout=10
        > git config --add remote.origin.fetch +refs/heads/*:refs/remotes/origin/* # timeout=10
        Avoid second fetch
        > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
        Checking out Revision 153bfc4ba64f7b9e3e7bcfa31210357949261197 (refs/remotes/origin/main)
        > git config core.sparsecheckout # timeout=10
        > git checkout -f 153bfc4ba64f7b9e3e7bcfa31210357949261197 # timeout=10
        Commit message: "Final-Project-Assessment-for-Scalefocus-Academy"
        First time build. Skipping changelog.
        [Pipeline] }
        [Pipeline] // stage
        [Pipeline] withEnv
        [Pipeline] {
        [Pipeline] stage
        [Pipeline] { (Validate)
        [Pipeline] sh
        + echo Download required binaries
        Download required binaries
        + curl -L -s https://dl.k8s.io/release/stable.txt
        + curl -LOs https://dl.k8s.io/release/v1.27.1/bin/linux/amd64/kubectl
        + chmod +x kubectl
        + curl -Os https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64
        + mv jq-linux64 jq
        + chmod +x jq
        [Pipeline] script
        [Pipeline] {
        [Pipeline] sh
        + /bin/bash -c ./kubectl get ns wp -o json | ./jq .status.phase -r
        Error from server (NotFound): namespaces "wp" not found
        [Pipeline] sh
        + ./kubectl create namespace wp
        namespace/wp created
        [Pipeline] }
        [Pipeline] // script
        [Pipeline] }
        [Pipeline] // stage
        [Pipeline] }
        [Pipeline] // withEnv
        [Pipeline] }
        [Pipeline] // node
        [Pipeline] End of Pipeline
        Finished: SUCCESS