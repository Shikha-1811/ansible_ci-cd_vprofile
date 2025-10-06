pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven3.9'
    }

    environment {
        ANSIBLE_KEY = '/var/lib/jenkins/.ssh/frontend-backend.pem'
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

        stage('Run Ansible Playbooks') {
            steps {
                sh '''
                echo "Running Ansible playbooks to set up servers and deploy WAR..."
                ansible-playbook -i ansible/inventory ansible/site.yaml --private-key $ANSIBLE_KEY
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Build or Deployment failed! Check console output.'
        }
    }
}

