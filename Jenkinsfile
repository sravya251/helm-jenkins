pipeline {

    agent any

    environment {
        RELEASE_NAME = "backend-release"
        CHART_PATH = "./backend-chart"
        NAMESPACE = "dev"
        AWS_REGION = "ap-south-1"
        CLUSTER_NAME = "eks-cluster"   // 🔥 change if different
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
                echo "Files:"
                ls -l
                '''
            }
        }

        stage('Configure AWS & Kubeconfig') {
            steps {
                withCredentials([
                    string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                    echo "Setting AWS credentials..."

                    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    export AWS_DEFAULT_REGION=$AWS_REGION

                    echo "Checking AWS identity..."
                    aws sts get-caller-identity

                    echo "Listing clusters..."
                    aws eks list-clusters --region $AWS_REGION

                    echo "Updating kubeconfig..."
                    aws eks update-kubeconfig --region $AWS_REGION --name $CLUSTER_NAME

                    echo "Checking Kubernetes connection..."
                    kubectl get nodes
                    '''
                }
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
