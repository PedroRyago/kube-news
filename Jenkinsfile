pipeline{
    agent any
    stages{
        stage('Build Docker Image'){
            steps{
                script{
                    dockerapp = docker.build("pedroryago/kube-news:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                script{
                    dockerapp.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials'){
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy K8S'){
            steps{
                withKubeConfig([credentialsId: 'kubeconfig']){
                    sh 'sed -i "s/{{TAG}}/${env.BUILD_ID}/g" ./k8s/deployment.yml'
                    sh 'kubectl apply -f ./k8s/deployment.yml'
                }
            }
        }
    }
}