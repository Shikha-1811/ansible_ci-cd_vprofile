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

        stage('Deploy WAR to Tomcat') {
            steps {
                sh '''
                # Copy WAR to temporary folder on remote server
                scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/frontend-backend.pem \
                target/vprofile-v2.war ubuntu@172.31.17.16:/tmp/vprofile-v2.war

                # Move WAR to Tomcat directory with sudo
                ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/frontend-backend.pem \
                ubuntu@172.31.17.16 "sudo mv /tmp/vprofile-v2.war /opt/tomcat/webapps/vprofile-v2.war"

                # Restart Tomcat
                ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/frontend-backend.pem \
                ubuntu@172.31.17.16 "sudo systemctl restart tomcat"
                '''
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i ~/ansible/inventory ~/ansible/site.yaml'
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

