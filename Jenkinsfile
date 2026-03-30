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

                git ''

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

                kubectl rollout status deployment/backend \

                -n $NAMESPACE

                '''

            }

        }
 
    }

}
 
