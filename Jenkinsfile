pipeline {
    agent any

    environment {
        // Safely pulls your admin login from the secure Jenkins credential store
        SOLACE_SEMP = credentials('solace_admin_creds')
    }

    stages {
        stage('Initialize Framework Workspace') {
            steps {
                // Securely downloads your playbooks and configs folder from GitHub
                checkout scm
            }
        }

        stage('Deploying to Dev Broker (Port 8080)') {
            steps {
                echo "Running configurations on Dev Broker..."
                sh '''
                ansible-playbook -i inventory/dev.ini playbooks/deploy_solace.yml \
                --extra-vars "semp_user=${SOLACE_SEMP_USR} semp_pass=${SOLACE_SEMP_PSW}"
                '''
            }
        }

        stage('Deploying to Production Broker (Port 8081)') {
            steps {
                echo "Running configurations on Production Broker..."
                sh '''
                ansible-playbook -i inventory/prod.ini playbooks/deploy_solace.yml \
                --extra-vars "semp_user=${SOLACE_SEMP_USR} semp_pass=${SOLACE_SEMP_PSW}"
                '''
            }
        }
    }
}
