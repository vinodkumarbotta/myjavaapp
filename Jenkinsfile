pipeline {
    agent any

    stages {
        stage('Build Code') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Test Code') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
