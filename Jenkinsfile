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
                echo "Stopping and removing any existing containers..."
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
                body: 'An error occurred during the build or deploy process.',
                cc: '',
                from: 'jenkins@example.local',
                replyTo: '',
                subject: "Pipeline Notification - Build #${BUILD_NUMBER}",
                to: 'mahdi@example.local'
            )
        }
    }


}