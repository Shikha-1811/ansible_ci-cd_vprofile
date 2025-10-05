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
                echo "Copying WAR to Tomcat server..."
                scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/frontend-backend.pem \
                target/vprofile-v2.war ubuntu@172.31.17.16:/tmp/vprofile-v2.war

                echo "Moving WAR to Tomcat folder with sudo..."
                ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/frontend-backend.pem \
                ubuntu@172.31.17.16 "sudo mv /tmp/vprofile-v2.war /opt/tomcat/webapps/vprofile-v2.war && sudo systemctl restart tomcat"
                '''
            }
        }

        stage('Run Ansible Playbooks') {
            steps {
                sh '''
                echo "Running Nginx & Tomcat configuration..."
                ansible-playbook -i ansible/inventory ansible/site.yaml --key-file /var/lib/jenkins/.ssh/frontend-backend.pem

                echo "Running DB configuration..."
                ansible-playbook -i ansible/inventory ansible/db-mysql.yaml --key-file /var/lib/jenkins/.ssh/db-mysql.pem
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Check logs!'
        }
    }
}

