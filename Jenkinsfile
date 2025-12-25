pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://54.172.33.250:9000/'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') { // Name configured in Jenkins
                    withCredentials([string(credentialsId: 'sonar-jenkins-token', variable: 'SONAR_TOKEN')]) {
                        sh """
                            mvn sonar:sonar \
                            -Dsonar.projectKey=reports \
                            -Dsonar.projectName=reports \
                            -Dsonar.host.url=$SONAR_HOST_URL \
                            -Dsonar.login=$SONAR_TOKEN \
                            -Dsonar.java.binaries=target/classes
                        """
                    }
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy to Tomcat Manager') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'tomcat-creds',
                    usernameVariable: 'TOMCAT_USER',
                    passwordVariable: 'TOMCAT_PASS'
                )]) {
                    sh '''
                        WAR_PATH=$(find target -name "*.war" | head -n 1)
                        echo "Deploying WAR: $WAR_PATH"
                        curl -u $TOMCAT_USER:$TOMCAT_PASS \
                        -T $WAR_PATH \
                        "http://100.27.191.247:8080/manager/text/deploy?path=/Rest-API&update=true"
                    '''
                }
            }
        }
    }
}
