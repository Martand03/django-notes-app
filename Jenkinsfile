@Library("Shared") _
pipeline {
    agent { label "AgentJ" }
    stages {
        stage("Hello") {
            steps {
                script {
                    hello()
                }
            }
        }
        stage("Code") {
            steps {
                script {
                    clone("https://github.com/Martand03/django-notes-app.git", "main")
                }
            }
        }
        stage("Build") {
            steps {
                script {
                    docker_build("django-notes-app", "latest")
                }
            }
        }
        stage("Test") {
            steps {
                echo "This is testing the code"
            }
        }
        stage("Push to DockerHub") {
            steps {
                echo "This is pushing the code to DockerHub"
                withCredentials([usernamePassword(credentialsId: "jenkins-docker",
                    passwordVariable: "dockerHubPass",
                    usernameVariable: "dockerHubUser")]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag django-notes-app:latest manik31/django-notes-app:latest"
                    sh "docker push manik31/django-notes-app:latest"
                    echo "Success in pushing Docker image to DockerHub"
                }
            }
        }
        stage("Deploy to EC2") {
            steps {
                echo "This is deploying the code to EC2"
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'EC2_KEY')]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no -i ${EC2_KEY} ec2-user@your-ec2-public-ip << EOF
                            docker pull manik31/django-notes-app:latest
                            docker-compose -f docker-compose.prod.yml up -d
                        EOF
                    """
                }
            }
        }
    }
}
