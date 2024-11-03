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
        echo "Deploying the code to EC2"
        withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'EC2_KEY')]) {
            // Use a temporary file to hold the SSH key securely
            writeFile(file: 'key.pem', text: "${EC2_KEY}")
            sh """
                chmod 600 key.pem 
                ssh -i key.pem ec2-user@13.126.214.40 << EOF
                    set -e  # Exit immediately if a command exits with a non-zero status
                    echo "Pulling the latest Docker image..."
                    docker pull manik31/django-notes-app:latest  
                    echo "Bringing down any running containers..."
                    docker-compose -f docker-compose.prod.yml down || true  
                    echo "Starting the application..."
                    docker-compose -f docker-compose.prod.yml up -d
                EOF
            """
            // Clean up the key file after use
            sh 'rm key.pem'
        }
    }
}


        
    }
}
