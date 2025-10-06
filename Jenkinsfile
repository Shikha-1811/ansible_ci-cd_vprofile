pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven3.9'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Shikha-1811/ansible_ci-cd_vprofile.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Copy WAR to App Server') {
            steps {
                sh '''
                echo "Copying WAR file to app server..."
                scp -o StrictHostKeyChecking=no -i /home/ubuntu/.ssh/frontend-backend.pem \
                target/vprofile-v2.war ubuntu@172.31.17.16:/tmp/vprofile-v2.war
                '''
            }
        }

        stage('Run Ansible Playbooks (Setup Servers)') {
            steps {
                sh '''
                echo "Running Ansible playbooks to set up environment..."
                ansible-playbook -i ansible/inventory ansible/site.yaml --private-key /home/ubuntu/.ssh/frontend-backend.pem
                '''
            }
        }

        stage('Restart Tomcat') {
            steps {
                sh '''
                echo "Restarting Tomcat to deploy app..."
                ssh -o StrictHostKeyChecking=no -i /home/ubuntu/.ssh/frontend-backend.pem \
                ubuntu@172.31.17.16 "sudo systemctl restart tomcat"
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Build or Deployment failed! Please check console output.'
        }
    }
}

