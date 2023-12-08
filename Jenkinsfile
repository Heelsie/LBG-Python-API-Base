pipeline {
    agent any
    stages {
        stage('Environment') {
            steps {
                script {
                    sh '''
                echo "You are  $(GIT_BRANCH) GIT Branch"
                '''
                }
                
           }
        }
        stage('Init') {
            steps {
                sh '''
                echo "You are  $(GIT_BRANCH) GIT Branch"
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.39 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm -f flask-app || echo "flask-app not running"
                docker rmi flask-app || echo "flask-app image already removed"
                docker stop mynginx || echo "mynginx not running"
                docker rm -f mynginx || echo "mynginx not running"
                docker rmi mynginx || echo "mynginx image already removed"
                docker network create new-network || true
                '''
           }
        }
        stage('Build') {
            steps {
                sh '''
                docker build -t heelsie/python-api -t heelsie/python-api:v${BUILD_NUMBER} .                
                docker build -t heelsie/nginx-jenk:latest -t heelsie/nginx-jenk:v${BUILD_NUMBER} ./nginx                 
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push heelsie/python-api
                docker push heelsie/python-api:v${BUILD_NUMBER}
                docker push heelsie/nginx-jenk:latest
                docker push heelsie/nginx-jenk:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.39 << EOF
                docker run -d --network new-network --name flask-app heelsie/python-api
                docker run -d -p 80:80 --name mynginx --network new-network heelsie/nginx-jenk:latest
                '''
            }
        }
        stage('Cleanup') {
            steps {
                sh '''
                docker system prune -f 
                '''
           }
        }
    }
}
