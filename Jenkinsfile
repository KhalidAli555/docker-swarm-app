pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "khalidali07/fastapi-app:${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/KhalidAli555/docker-swarm-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-login', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push $DOCKER_IMAGE"
            }
        }

        stage('Deploy to Swarm') {
            steps {
                sh "IMAGE_TAG=${env.BRANCH_NAME}-${env.BUILD_NUMBER} DOCKERHUB_USER=khalidali07 docker stack deploy -c docker-compose.yml fastapiapp"
            }
        }
    }

    post {
        success {
            emailext(
                to: 'mughalkhalid354@gmail.com',
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Good news!\nThe build succeeded.\n\nCheck it here: ${env.BUILD_URL}"
            )
        }
        failure {
            emailext(
                to: 'mughalkhalid354@gmail.com',
                subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The build failed.\n\nCheck logs: ${env.BUILD_URL}"
            )
        }
        always {
            echo "✅ Pipeline completed"
            sh 'docker logout'
        }
    }
}
