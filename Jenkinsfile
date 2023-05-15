pipeline {
    agent any 
    stages {
        stage('Validate') { 
            steps {
                // Donwload required binaries
                sh '''
                echo "Download required binaries"
                curl -LOs "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
                chmod +x kubectl
                curl -Os https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64
                mv jq-linux64 jq && chmod +x jq
                '''
                // Check if wp namespace exists and create if it doesn't
                script {
                    def wp_namespace = sh(script: "/bin/bash -c './kubectl get ns wp -o json | ./jq .status.phase -r'")
                    if (wp_namespace) {
                            echo "wp namespace already created"
                            exist()
                    } else {
                            sh './kubectl create namespace wp'
                    }
                }
            }
        }
    }
}