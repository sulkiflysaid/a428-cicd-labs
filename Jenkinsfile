pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        message: 'Lanjutkan ke tahap Deploy?',
                        ok: 'Proceed',
                        submitter: 'user',
                        parameters: [boolean(defaultValue: false, description: 'Pilih "Proceed" untuk melanjutkan atau "Abort" untuk menghentikan.', name: 'approval')]
                    )
                    env.approval = userInput
                }
            }
        }
        stage('Deploy') {
            when {
                expression {
                    // Tahap Deploy hanya dijalankan jika "approval" bernilai true
                    return env.approval == true
                }
            }
            steps {
                sh './jenkins/scripts/deliver.sh'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}