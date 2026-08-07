pipeline {
    agent any

    environment {
        VENV_PATH = "/home/nixk/AnsibleSolace/SolaceCICD/solace_env/bin"
        SOLACE_SEMP = credentials('solace_admin_creds')
    }

    stages {
        stage('Initialize Framework Workspace') {
            steps {
                checkout scm
            }
        }

        stage('Deploying to Dev (Port 8080)') {
            when {
                // Matches dev or origin/dev
                branch pattern: ".*dev", comparator: "REGEXP"
            }
            steps {
                sh """
                ${VENV_PATH}/ansible-playbook -i inventory/dev.ini playbooks/deploy_solace.yml \
                --extra-vars "semp_user=${SOLACE_SEMP_USR} semp_pass=${SOLACE_SEMP_PSW}"
                """
            }
        }

        stage('Deploying to Production (Port 8081)') {
            when {
                // Matches main or origin/main
                branch pattern: ".*main", comparator: "REGEXP"
            }
            steps {
                sh """
                ${VENV_PATH}/ansible-playbook -i inventory/prod.ini playbooks/deploy_solace.yml \
                --extra-vars "semp_user=${SOLACE_SEMP_USR} semp_pass=${SOLACE_SEMP_PSW}"
                """
            }
        }
    }
}
