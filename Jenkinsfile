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
                        parameters: [choice(choices: ['Proses', 'Batal'], description: 'Pilih "Proses" untuk melanjutkan atau "Batal" untuk menghentikan.', name: 'approval')]
                    )
                    if (userInput == 'Proses') {
                        env.approval = true
                    } else {
                        env.approval = false
                    }
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
