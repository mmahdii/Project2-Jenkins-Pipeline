pipeline {
    agent {
        label 'slave-02-tag'
    } 
    environment {
        APP_NAME = "node-app"
        APP_PORT = 3000
        DOCKER_IMAGE = "${APP_NAME}:${BUILD_NUMBER}"
    }
    stages{
        stage("Build"){
            steps {
                echo "Building Docker image: ${DOCKER_IMAGE}"
                sh "docker build -t $DOCKER_IMAGE . "
            }
        }
        stage("Deploy"){
            when {
                branch 'main'
            }
            steps {
                echo "Stopping and removing any existing containers ..."
                                sh '''
                docker rm -f $(docker ps -qa --filter "name=${APP_NAME}") || true
                echo "Cleanup done!"
                '''

                echo "Deploying Docker container..."
                sh "docker run -dit --name ${APP_NAME} -p ${APP_PORT}:${APP_PORT} ${DOCKER_IMAGE}"
                
                echo "Current running containers:"
                sh "docker ps --filter 'name=${APP_NAME}'" 
                
            }
        }

    }

post { 
    always {
        echo 'Pipeline finished. Sending notification...'
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
        echo 'Build and Deploy completed successfully!'
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
        echo 'Pipeline failed!'
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
        echo 'Pipeline was aborted!'
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