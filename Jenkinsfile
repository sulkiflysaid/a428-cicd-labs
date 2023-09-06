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
        input(message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed', submitter: 'user', parameters: [boolean(defaultValue: false, description: 'Lanjutkan ke tahap Deploy?', name: 'approval')])
            }
        }
        stage('Deploy') {
            when {
                expression {
                    // Tahap Deploy hanya dijalankan jika "approval" bernilai true
                    return params.approval == true
                }
            }
            steps {
                sh './jenkins/scripts/deliver.sh'
                sh './jenkins/scripts/kill.sh'
            }
    }
}