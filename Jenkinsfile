pipeline {
    agent any

    parameters {
        string(name: 'AWS_CREDENTIALS_ID', defaultValue: 'latest', description: 'Provide AWS credentials ID')
        string(name: 'ClusterName', defaultValue: 'latest', description: 'Provide ClusterName')
        string(name: 'AWS_REGION', defaultValue: 'latest', description: 'Provide Region, EX: us-west-2')
    }

    environment {
        GIT_REPO_URL = 'https://github.com/LemonHQ/infrastructure.git'
    }

    stages {
        stage('Helm Check') {
            steps {
                script {
                    def helmInstalled = sh(script: 'command -v helm &> /dev/null', returnStatus: true) == 0
                    if (helmInstalled) {
                        echo "Helm is already installed."
                    } else {
                        echo "Helm is not installed."
                        sh 'curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3'
                        sh 'chmod +x get_helm.sh'
                        sh './get_helm.sh'
                        sh 'rm get_helm.sh'
                        sh 'helm version'
                    }
                }
            }
        }
        stage('Deploy ArgoCD to Cluster') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding', 
                    credentialsId: params.AWS_CREDENTIALS_ID, 
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    script {
                        sh '''
                            aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}
                            aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
                            aws configure set region ${AWS_REGION}
                            aws s3 ls
                            # Uncomment the following lines to deploy ArgoCD
                            # aws eks update-kubeconfig --name ${ClusterName} --region ${AWS_REGION}
                            # helm repo add argo https://argoproj.github.io/argo-helm
                            # helm repo update
                            # helm install argocd argo/argo-cd --namespace argocd --create-namespace -f argocd/values.yaml
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
