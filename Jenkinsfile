pipeline {
  agent any

    environment {
        GIT_REPO_URL = 'https://github.com/Saqeeb1234/jenkins-k8s.git'
    }

    stages{
        stage('clone repo'){
            steps{
              script{
                sh'''
                git clone "${GIT_REPO_URL}"
                '''
              }
            }
        }
    }

    stage('Deploy argcd to cluster'){
        steps{
            script{
                withKubeConfig([credentialsId: 'minikube-kubeconfig']){
                    sh'''
                    helm repo add argo https://argoproj.github.io/argo-helm
                    helm repo update
                    helm upgrade --install argocd ${HELM_CHART_PATH} --namespace argocd --create-namespace -f argocd/values.yaml
                    '''
                }
            }
        }
    }
}
