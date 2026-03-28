pipeline {
    agent any

    environment {
        DOCKER_USER = 'sujaygope9939'
    }

    tools {
        maven 'maven'
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/Sujay9939/All-in-one-CI-CD'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                docker build -t $DOCKER_USER/myapp:latest .
                docker push $DOCKER_USER/myapp:latest
                """
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push') {
            steps {
                sh """
                docker push $DOCKER_USER/myapp:$BUILD_NUMBER
                """
            }
        }

        stage('Deploy to K8s') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                    export KUBECONFIG=$KUBECONFIG
                    kubectl get nodes
                    kubectl apply -f deployment.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Deployment Success 🚀"
        }
        failure {
            echo "Deployment Failed ❌"
        }
    }
}
