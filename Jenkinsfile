pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main") {
                        sh '''
                        kubectl create namespace prod || echo "Namespace prod already exists"
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        kubectl create namespace dev || echo "Namespace dev already exists"
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }
        
         stage('Build') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main") {
                        sh '''
                        docker build -t heelsie/lbgapp:latest -t heelsie/lbgapp:prod-v${BUILD_NUMBER} .
                        docker build -t heelsie/lbg-nginx:latest -t heelsie/lbg-nginx:prod-v${BUILD_NUMBER} .
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        docker build -t heelsie/lbgapp:latest -t heelsie/lbgapp:dev-v${BUILD_NUMBER} .
                        docker build -t heelsie/lbg-nginx:latest -t heelsie/lbg-nginx:dev-v${BUILD_NUMBER} .
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main") {
                sh '''
                docker push heelsie/lbgapp:latest
                docker push heelsie/lbgapp:prod-v${BUILD_NUMBER}
                docker push heelsie/lbg-nginx:latest
                docker push heelsie/lbg-nginx:prod-v${BUILD_NUMBER}
                '''
            
            } else if (env.GIT_BRANCH == "origin/dev") {
                sh '''
                docker push heelsie/lbgapp:latest
                docker push heelsie/lbgapp:dev-v${BUILD_NUMBER}
                docker push heelsie/lbg-nginx:latest
                docker push heelsie/lbg-nginx:dev-v${BUILD_NUMBER}
                '''
                } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                    script {
                        if (env.GIT_BRANCH == "origin/main") {
                    sh '''
                    kubectl apply -n prod -f ./kubernetes
                    kubectl set image deployment/lbg-deployment flask-container=heelsie/lbgapp:v${BUILD_NUMBER} -n prod
                    kubectl set image deployment/nginx-deployment nginx-container=heelsie/lbg-nginx:prod-v${BUILD_NUMBER} -n prod
                    '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                    kubectl apply -n dev -f ./kubernetes
                    kubectl set image deployment/lbg-deployment flask-container=heelsie/lbgapp:v${BUILD_NUMBER} -n dev
                    kubectl set image deployment/nginx-deployment nginx-container=heelsie/lbg-nginx:dev-v${BUILD_NUMBER} -n dev
                    '''
                    } else {
                        sh '''
                            echo "Branch not recognised"
                            '''
                    }
                }
            }
        }

        stage('CleanUp') {
            steps {
                sh '''
                docker system prune -f 
                '''
            }
        }
    }
}
