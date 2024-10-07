pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'gh-token', url: 'https://github.com/kkochel/abcd-student.git', branch: 'main'
                }
            }
        }
        stage('Example - Hello Pawle!') {
            steps {
                echo 'Hello Pawle!'
                sh 'ls -la'
            }
        }
    }
}