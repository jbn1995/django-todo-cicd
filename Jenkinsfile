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
                    sh 'sudo docker build -t jabin95/django-todo:02 .'
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([usernamePassword(credentialsId: 'dockerID', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh 'sudo docker login -u $USERNAME -p $PASSWORD'
                    }
                    sh 'sudo docker push jabin95/django-todo:02'
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
	post {
        success {
            echo 'Build and deployment completed successfully!'
            
            mail to: 'noushadhasan3395@gmail.com', subject: 'Build Success', body: "Todo-app build was successful!"
        }
        failure {
            echo 'Build or deployment failed.'
           
            mail to: 'noushadhasan3395@gmail.com', subject: 'Build Failed', body: "Todo-app build failed."
        }
        always {
            echo 'Clean Up the workspace.'
            
            cleanWs()  // Cleans up the workspace
        }
    }
}
