pipeline {
    agent {
        label 'slave-02-tag'
    } 
    stages{
        stage("Build"){
            steps {
                sh "docker build -t node-app:$BUILD_NUMBER . "
            }
        }
        stage("Deploy"){
            steps {
                sh "docker rm -f $(docker ps -qa)"
                sh "docker run -dit --name node-app -p 3000:3000 node-app:$BUILD_NUMBER"
                echo "Deploying ..."
            }
        }
    }
}