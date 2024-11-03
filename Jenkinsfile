@Library("Shared") _
pipeline{
    agent {label "AgentJ"}
    stages{
        stage("Hello"){
            steps{
                script{
                    hello()
                }
            }
        }
        stage("Code"){
            steps{
                script{
                    clone("https://github.com/Martand03/django-notes-app.git","main")
                }
            }
        }
        stage("Build"){
           steps {
                script{
                    docker_build("django-notes-app","latest")
                }
            }
        }
        stage("Test"){
            steps{
                echo "This is testting the code"
            }
        }
        stage("Push to dockerhub"){
            steps{
                echo "This is pushing the code to dockerhub"
                withCredentials([usernamePassword('credentialsId':"jenkins-docker",
                    passwordVariable:"dockerHubPass",
                    usernameVariable:"dockerHubUser")]){
                    bat "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    bat "docker image tag django-notes-app:latest manik31/django-notes-app:latest"
                    bat "docker push manik31/django-notes-app:latest"
                    echo "Success in pushing docker image to dockerHub"
                    }
            }
        }
        stage("Deploy to EC2") {
            steps {
                script {
                    // Define the EC2 instance IP and SSH credentials
                    def ec2Ip = "13.126.214.40"
                    def sshKeyPath = "C:\\Users\\marta\\Downloads\\keypair1.pem" // Update this to your private key path
                    def remoteUser = "ec2-user" // Default user for Amazon Linux

                    // SSH command to deploy the application
                    sh """
                        ssh -i ${sshKeyPath} ${remoteUser}@${ec2Ip} '
                        sudo docker pull manik31/django-notes-app:latest
                        sudo docker run -d -p 8000:8000 manik31/django-notes-app:latest
                        '
                    """
                }
            }
        }


        
    }
}
