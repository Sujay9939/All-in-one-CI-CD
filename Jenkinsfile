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
                git branch: 'main', url: 'https://github.com/Sujay9939/All-in-one-CI-CD'
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
                    sh 'mvn sonar:sonar || true'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_USER/myapp:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-cred',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push') {
            steps {
                sh 'docker push $DOCKER_USER/myapp:latest'
            }
        }

        stage('Deploy to K8s') {
          steps {
             withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh '''
            echo "Current directory:"
            pwd

            echo "Files in workspace:"
            ls -l

            export KUBECONFIG=$KUBECONFIG

            kubectl apply -f deployment.yaml --validate=false
            kubectl apply -f service.yaml --validate=false
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
