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

        stage('Deploy to Tomcat Manager') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'tomcat-creds',
                    usernameVariable: 'TOMCAT_USER',
                    passwordVariable: 'TOMCAT_PASS'
                )]) {
                    sh '''
                        curl -u $TOMCAT_USER:$TOMCAT_PASS \
                        -T /var/lib/jenkins/.m2/repository/koddas/web/war/wwp/1.0.0/wwp-1.0.0.war \
                        "http://3.92.4.131:8080/manager/text/deploy?path=/Rest-API&update=true"
                    '''
                }
            }
        }
    }
}
