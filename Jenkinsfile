pipeline {
    agent {
        node {
            label 't054-runner'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'ca-git-access', branch: 'devp', url: "https://git.cloudavise.com/visops/t054/sample-javafullstack-app.git"
            }
        }

        stage('prepare') {
            steps {
                sh "ansible-vault decrypt --vault-id /tmp/vault-id new_key.pem"
                sh "chmod 400 demo.pem"
                sh "ansible-playbook -i inventory fullstack-deploy.yml"
            }
        }
    }

    post {
        always {
            script {
                userInput = input(
                    id: 'userConfirmation',
                    message: 'Confirm to proceed with Docker cleanup',
                    parameters: [
                        [$class: 'BooleanParameterDefinition', defaultValue: false, description: '', name: 'PROCEED']
                    ]
                )
                if (userInput.PROCEED) {
                    sh 'docker-compose down'
                }
            }
        }
    }
}
