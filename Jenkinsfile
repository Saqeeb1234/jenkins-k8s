pipeline {
  agent any
    
    parameters{
        string(name: 'AWS_CREDENTIALS_ID', defaultValue: 'latest', description: 'Provide AWS credtials ID')
        string(name: 'ClusterName', defaultValue: 'latest', description: 'Provide ClusterName')
        string(name: 'AWS_REGION', defaultValue: 'latest', description: 'Provide Region, EX: us-west-2')
    }

    environment {
        // KUBECONFIG = credentials('minikube-kubeconfig')
        GIT_REPO_URL = 'https://github.com/LemonHQ/infrastructure.git'
    }

    stages {
        // stage('Checkout') {
        //     steps {
        //         script {
        //             dir ("Cluster-bootstrap")
        //             {
        //                 git branch: "${GIT_BRANCH}",
        //                     credentialsId: "infra-repo-credentials", 
        //                     url: "https://git-codecommit.ap-south-1.amazonaws.com/v1/repos/tf-code-repo-01"
        //             }
        //         }
        //     }
        // }
        stage('Helm Check'){
            script{
                if (command -v helm &> /dev/null){
                   sh "echo Helm is already installed."                                      
                }else{
                sh '''
                    echo "Helm is not installed."
                    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
                    chmod +x get_helm.sh
                    ./get_helm.sh
                    rm get_helm.sh
                    helm version
                '''
                }
            }
        }
        stage('Deploy argcd to cluster'){
            steps{
                script{
                    withCredentials([[
                    $class: "AmazonWebServicesCredentialsBinding",
                    credentialsId: params.AWS_CREDENTIALS_ID
                    // accessKeyVariable: "AWS_ACCESS_KEY_ID",
                    // secretKeyVariable: "AWS_SECRET_ACCESS_KEY"
                    ]]){
                        sh'''
                        aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}
                        aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
                        aws configure set region ${AWS_REGION}
                        aws s3 ls
                        // aws eks update-kubeconfig --name ${EKS_CLUSTER_NAME} --region ${AWS_REGION}
                        helm repo add argo https://argoproj.github.io/argo-helm
                        helm repo update
                        helm install argocd argo/argo-cd --namespace argocd --create-namespace -f argocd/values.yaml
                        '''
                    }
                }
            }
        }
    }
}
