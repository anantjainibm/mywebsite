pipeline {
    agent any

    environment {
        DEPLOY_USER = 'ubuntu'
        DEPLOY_HOST = '3.109.1.120'
        DEPLOY_KEY = credentials('ec2-ssh-key')  // Make sure this ID matches your Jenkins SSH key
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/anantjainibm/mywebsite.git'
            }
        }

        stage('Build') {
    steps {
        withMaven(maven: 'maven') {
            sh 'mvn clean package'
        }
    }
}
        stage('Test') {
    steps {
        withMaven(maven: 'maven') {
            sh 'mvn test'
        }
    }
}
        stage('Deploy') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                        scp target/*.war ubuntu@3.109.1.120:/home/ubuntu/
                        ssh ubuntu@3.109.1.120 'sudo mv /home/ubuntu/*.war /var/lib/tomcat/webapps/ && sudo systemctl restart tomcat'
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Build and Deployment Successful!'
        }
        failure {
            echo '❌ Something went wrong!'
        }
    }
}
