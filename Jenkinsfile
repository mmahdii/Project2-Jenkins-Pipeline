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
            when {
                branch 'main'
            }
            steps {
                sh '''
                docker rm -f $(docker ps -qa)
                echo "Cleanup done!"
                echo "Deploying ..."
                docker run -dit --name node-app -p 3000:3000 node-app:$BUILD_NUMBER
                docker ps
                '''
                
            }
        }

    }

    post { 
        always { 
            echo 'I will always say Hello again!'
        }
    }


}