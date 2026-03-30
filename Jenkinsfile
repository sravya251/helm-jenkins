pipeline {

    agent any
 
    environment {
        RELEASE_NAME = "backend-release"
        CHART_PATH = "./backend-chart"
        NAMESPACE = "dev"
    }
 
    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/sravya251/helm-jenkins.git'
            }
        }

        stage('Configure Kubeconfig') {
            steps {
                sh '''
                echo "Configuring kubeconfig..."
                aws eks update-kubeconfig \
                --region ap-south-1 \
                --name <your-cluster-name>
                '''
            }
        }
 
        stage('Helm Lint') {
            steps {
                sh 'helm lint $CHART_PATH'
            }
        }
 
        stage('Helm Package') {
            steps {
                sh 'helm package $CHART_PATH'
            }
        }
 
        stage('Helm Deploy') {
            steps {
                sh '''
                helm upgrade --install $RELEASE_NAME $CHART_PATH \
                --namespace $NAMESPACE \
                --create-namespace
                '''
            }
        }
 
        stage('Deployment Validation') {
            steps {
                sh '''
                kubectl rollout status deployment/backend -n $NAMESPACE
                '''
            }
        }
    }
}
