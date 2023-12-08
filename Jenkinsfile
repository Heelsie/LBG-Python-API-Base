pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.39 << EOF
                docker stop flask-app || echo "flask-app not running"
                docker rm flask-app || echo "flask-app not running"
                docker rmi flask-app || echo "Image already removed"
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
                docker run -d -p 80:8080 --name flask-app heelsie/python-api
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
