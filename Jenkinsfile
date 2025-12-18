pipeline {
    agent any

    // tools {
    //     maven 'Maven'
    // }

    stages {
        stage('Build Code') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}
