pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'anushreegm12/python-app:latest' 
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/anushreegm12/python-pipeline.git', branch: 'main'
            }
        }

        stage('Package App') {
            steps {
                sh 'zip -r app.zip . -x "*.git*"'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get services'
            }
        }
        
        stage('Scale Up') {
            steps{
                sh 'kubectl scale deployment python-app --replicas=3'
                sh 'kubectl get pods'
            }
        }

        stage('Scale Down') {
            steps {
                sh 'kubectl scale deployment python-app --replicas=1'
                sh 'kubect get pods'
            }
        }
        
    }

    post {
        success {
            echo '🎉 Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
