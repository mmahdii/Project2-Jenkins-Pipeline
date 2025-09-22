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
                echo "Deploying ..."
            }
        }
    }
}