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
                '''
           }
        }
        stage('Build') {
            steps {
                sh '''
                docker build -t heelsie/python-api -t heelsie/python-api:v${BUILD_NUMBER} .                  
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push heelsie/python-api
                docker push heelsie/python-api:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.39 << EOF
                docker run -d -p 80:8080 --name flask-app heelsie/python-api
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
