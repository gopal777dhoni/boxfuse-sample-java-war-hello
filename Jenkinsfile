pipeline {
    agent any

    stages {
        stage('clone repository') {
            steps {
                git 'https://github.com/gopal777dhoni/boxfuse-sample-java-war-hello.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("gopal777dhoni/static-website")
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    dockerImage.run('-p 80:80')
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            script {
                def containers = docker.ps('-q')
                if (containers) {
                    docker.stop(containers)
                    docker.rm(containers)
                }
                def images = docker.images('-q')
                if (images) {
                    docker.rmi(images)
                }
            }
        }
    }
}

