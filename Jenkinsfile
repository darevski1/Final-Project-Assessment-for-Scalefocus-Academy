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
                    def wp_namespace = sh(returnStdout: true, script: './kubectl get ns wp -o jsonpath=\'{.status.phase}\'');
                    println(wp_namespace)
                    if (wp_namespace != 'Active') {
                            sh './kubectl create namespace wp'
                    } else {
                            echo 'wp namespace already created'
                    }
                }
            }
        }
        stage('Install WordPress helm Chart') {
            steps {
                // Install helm binary
                sh '''
                curl -l https://get.helm.sh/helm-v3.12.0-linux-amd64.tar.gz
                tar zxvf helm-v3.12.0-linux-amd64.tar.gz
                '''
                // Install WordPress helm chart
                sh '''
                ./linux-amd64/helm dependency build ./helm/wordpress/
                ./linux-amd64/helm upgrade -i -f ./helm/wordpress/values.yaml final-project-wp-scalefocus ./helm/wordpress -n wp  
                '''
            }
        }
        stage('Make WordPress available') {
            steps {
                sh '''
                ./kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 8000:80 &
                echo "WordPress URL: http://127.0.0.1//"
                echo "WordPress Admin URL: http://127.0.0.1//admin"
                '''
            }
        }
        stage('Browse WordPress site') {
            steps {
                sh '''
                curl -v http://final-project-wp-scalefocus-wordpress.wp.svc.cluster.local
                '''
            }
        }
    }
}