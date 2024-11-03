@Library("Shared") _
pipeline{
    agent {label "AgentJ"}
    environment {
        // Set your EC2 details here
        EC2_USER = 'ec2-user' // Change this if your user is different
        EC2_HOST = '13.126.214.40' // Replace with your EC2 instance IP or hostname
        DOCKER_IMAGE = 'manik31/django-notes-app:latest' // Replace with your Docker image name
        PRIVATE_KEY = credentials('ec2-ssh-key') // Assuming you are using Jenkins credentials to manage your private key
    }
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
                    // Use SSH to connect to the EC2 instance and run commands
                    def deployCommand = """
                        ssh -o StrictHostKeyChecking=no -i ${PRIVATE_KEY} ${EC2_USER}@${EC2_HOST} << 'ENDSSH'
                        # Pull the Docker image
                        docker pull ${DOCKER_IMAGE}

                        # Stop and remove existing container if it exists
                        docker rm -f my_app_container || true

                        # Run the Docker container
                        docker run -d --name my_app_container -p 8000:8000 ${DOCKER_IMAGE}
                        ENDSSH
                    """

                    // Execute the deployment command
                    sh deployCommand
                }
            }
        }


        
    }
}
