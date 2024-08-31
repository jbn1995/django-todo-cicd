pipeline {
    agent any
    
    stages{
        stage('Pull code from github'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/develop']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/jbn1995/django-todo-cicd.git']]])
            
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t jabin95/django-todo:02 .'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([usernamePassword(credentialsId: 'dockerID', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh 'docker login -u $USERNAME -p $PASSWORD'
                    }
                    sh 'docker push jabin95/django-todo:02'
                }
            }
        }
        stage('Deploy to k8s') {
            steps {
                script {
                    // Point kubectl to Minikube's context
                    sh 'kubectl config use-context minikube'
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                    
                }
            }
        }
    }
}
