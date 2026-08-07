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

        stage('Deploying to Production (Port 8081)') {
            when {
                branch 'main'
            }
            steps {
                // Ensure NO '--check' flag is active in this execution block
                sh """
                ${VENV_PATH}/ansible-playbook -i inventory/prod.ini playbooks/deploy_solace.yml \
                --extra-vars "semp_user=${SOLACE_SEMP_USR} semp_pass=${SOLACE_SEMP_PSW}"
                """
            }
        }
    }
}
