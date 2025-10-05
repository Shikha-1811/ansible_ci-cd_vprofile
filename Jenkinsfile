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

        stage('Copy WAR to Tomcat Server') {
            steps {
                sh '''
                echo "Copying WAR file to Tomcat server..."
                scp -i /var/lib/jenkins/.ssh/frontend-backend.pem \
                target/vprofile-v2.war \
                ubuntu@172.31.17.16:/opt/tomcat/webapps/vprofile-v2.war
                '''
            }
        }

        stage('Deploy with Ansible') {
            steps {
                sh '''
                echo "Running Ansible Playbook..."
                ansible-playbook -i ~/ansible/inventory ~/ansible/site.yaml
                '''
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

