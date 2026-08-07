pipeline {
    agent any

    environment {
        // Securely pulls your admin login from the local Jenkins credential store
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
                // Move execution context directly into the playbooks folder
                dir('playbooks') {
                    sh '''
                    ansible-playbook -i ../inventory/dev.ini deploy_solace.yml \
                    --extra-vars "semp_user=${SOLACE_SEMP_USR} semp_pass=${SOLACE_SEMP_PSW}"
                    '''
                }
            }
        }

        stage('Deploying to Production Broker (Port 8081)') {
            steps {
                echo "Running configurations on Production Broker..."
                // Move execution context directly into the playbooks folder
                dir('playbooks') {
                    sh '''
                    ansible-playbook -i ../inventory/prod.ini deploy_solace.yml \
                    --extra-vars "semp_user=${SOLACE_SEMP_USR} semp_pass=${SOLACE_SEMP_PSW}"
                    '''
                }
            }
        }
    }
}
