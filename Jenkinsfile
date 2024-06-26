pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                      docker.build('my-app:latest')
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'npm test'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    docker.withRegistry('https://<account-id>.dkr.ecr.<region>.amazonaws.com', 'ecr:login') {
                docker.image('my-app:latest').push("${env.BUILD_NUMBER}")
                }
            }
        }
    }
stage('Deploy to EC2') {
    steps {
        sshagent (credentials: ['ec2-ssh-key']) {
            sh '''
            ssh -o StrictHostKeyChecking=no ec2-user@<ec2-instance-ip> 'docker pull <account-id>.dkr.ecr.<region>.amazonaws.com/my-app:${BUILD_NUMBER}'
            ssh -o StrictHostKeyChecking=no ec2-user@<ec2-instance-ip> 'docker run -d -p 80:3000 <account-id>.dkr.ecr.<region>.amazonaws.com/my-app:${BUILD_NUMBER}'
            '''
        }
    }
}
post {
    always {
        mail to: 'team@example.com',
             subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
             body: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]': ${currentBuild.currentResult}\n\nCheck console output at ${env.BUILD_URL}"
    }
}
}

