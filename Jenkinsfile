pipeline {

    agent any

    environment {
        RELEASE_NAME = "backend-release"
        CHART_PATH = "./backend-chart"
        NAMESPACE = "dev"
        AWS_REGION = "ap-south-1"
        CLUSTER_NAME = "eks-cluster"   // 🔥 CHANGE THIS if your cluster name is different
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/sravya251/helm-jenkins.git'
            }
        }

        stage('Debug Workspace') {
            steps {
                sh '''
                echo "Current directory:"
                pwd
                echo "Listing files:"
                ls -l
                '''
            }
        }

        stage('Configure AWS & Kubeconfig') {
            steps {
                sh '''
                echo "Checking AWS CLI..."
                aws --version

                echo "Checking AWS Identity..."
                aws sts get-caller-identity

                echo "Listing EKS clusters..."
                aws eks list-clusters --region $AWS_REGION

                echo "Configuring kubeconfig..."
                aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME

                echo "Testing Kubernetes connection..."
                kubectl get nodes
                '''
            }
        }

        stage('Helm Lint') {
            steps {
                sh '''
                echo "Linting Helm chart..."
                helm lint $CHART_PATH
                '''
            }
        }

        stage('Helm Package') {
            steps {
                sh '''
                echo "Packaging Helm chart..."
                helm package $CHART_PATH
                '''
            }
        }

        stage('Helm Deploy') {
            steps {
                sh '''
                echo "Deploying application..."
                helm upgrade --install $RELEASE_NAME $CHART_PATH \
                --namespace $NAMESPACE \
                --create-namespace
                '''
            }
        }

        stage('Deployment Validation') {
            steps {
                sh '''
                echo "Validating deployment..."
                kubectl get pods -n $NAMESPACE
                kubectl rollout status deployment/backend -n $NAMESPACE
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}
