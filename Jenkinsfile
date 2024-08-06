pipeline {
  agent any

    environment {
        KUBECONFIG = credentials('minikube-kubeconfig')
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

        stage('Deploy argcd to cluster'){
            steps{
                script{
                    withEnv(["KUBECONFIG=${KUBECONFIG}"]){
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
}
