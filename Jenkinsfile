pipeline {
    agent any

    environment {
        // Loads your admin credentials securely from the local Jenkins vault
        SOLACE_SEMP = credentials('solace_admin_creds')
    }

    stages {
        stage('Initialize Framework Workspace') {
            steps {
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
