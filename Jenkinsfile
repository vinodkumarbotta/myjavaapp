pipeline {
    agent any

    tools {
        maven 'maven-3'
    }

    stages {
        stage('Build Code') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}
