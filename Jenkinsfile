pipeline {
    agent { label 'slave-02-tag' }

    environment {
        APP_NAME = "node-app"
        APP_PORT = 3000
        DOCKER_IMAGE = "${APP_NAME}:${BUILD_NUMBER}"
        NEXUS_HOSTED_DOCKER_REPO_URL = "192.168.4.142:8082"
        NEXUS_CREDENTIAL = credentials("nexus-credential")
        NEXUS_CREDENTIAL_USER = "${NEXUS_CREDENTIAL_USR}"
        NEXUS_CREDENTIAL_PASSWORD = "${NEXUS_CREDENTIAL_PSW}"

    }

    stages {
        stage("Build & Push Docker") {
            steps {
 
                echo "Building Docker image: ${DOCKER_IMAGE}"
                
                sh """
                    docker build -t ${DOCKER_IMAGE} .
                    docker logout ${NEXUS_HOSTED_DOCKER_REPO_URL}
                    docker login ${NEXUS_HOSTED_DOCKER_REPO_URL} -u ${NEXUS_CREDENTIAL_USER} -p ${NEXUS_CREDENTIAL_PASSWORD}
                    docker tag ${DOCKER_IMAGE} ${NEXUS_HOSTED_DOCKER_REPO_URL}/${DOCKER_IMAGE}
                    docker push ${NEXUS_HOSTED_DOCKER_REPO_URL}/${DOCKER_IMAGE}
                """
            }
        }

        stage("Deploy") {
            when { branch 'main' }
            steps {
                echo "Stopping and removing existing containers..."
                sh """
                    docker rm -f \$(docker ps -qa --filter "name=${APP_NAME}") || true
                    echo "Cleanup done!"
                """

                echo "Deploying Docker container..."
                sh """
                    docker rmi -f \$(docker image ls -q) || true
                    docker logout ${NEXUS_HOSTED_DOCKER_REPO_URL}
                    docker login ${NEXUS_HOSTED_DOCKER_REPO_URL} -u ${NEXUS_CREDENTIAL_USER} -p ${NEXUS_CREDENTIAL_PASSWORD}              
                    docker pull ${NEXUS_HOSTED_DOCKER_REPO_URL}/${DOCKER_IMAGE}
                    docker run -dit --name ${APP_NAME} -p ${APP_PORT}:${APP_PORT} ${NEXUS_HOSTED_DOCKER_REPO_URL}/${DOCKER_IMAGE}
                    docker ps --filter "name=${APP_NAME}"
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Sending notification..."
            mail(
                bcc: '',
                body: "Pipeline finished for Build #${BUILD_NUMBER}.",
                cc: '',
                from: 'jenkins@example.local',
                replyTo: '',
                subject: "Pipeline Notification - Build #${BUILD_NUMBER}",
                to: 'mahdi@example.local'
            )
        }

        success {
            echo "Build and Deploy completed successfully!"
            mail(
                bcc: '',
                body: "Pipeline succeeded for Build #${BUILD_NUMBER}.",
                cc: '',
                from: 'jenkins@example.local',
                replyTo: '',
                subject: "SUCCESS - Build #${BUILD_NUMBER}",
                to: 'mahdi@example.local'
            )
        }

        failure {
            echo "Pipeline failed!"
            mail(
                bcc: '',
                body: "Pipeline failed during Build #${BUILD_NUMBER}. Please check the logs.",
                cc: '',
                from: 'jenkins@example.local',
                replyTo: '',
                subject: "FAILURE - Build #${BUILD_NUMBER}",
                to: 'mahdi@example.local'
            )
        }

        aborted {
            echo "Pipeline was aborted!"
            mail(
                bcc: '',
                body: "Pipeline was aborted for Build #${BUILD_NUMBER}.",
                cc: '',
                from: 'jenkins@example.local',
                replyTo: '',
                subject: "ABORTED - Build #${BUILD_NUMBER}",
                to: 'mahdi@example.local'
            )
        }
    }
}
