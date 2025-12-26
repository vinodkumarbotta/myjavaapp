pipeline {
    agent any

    environment {
        ARTIFACTORY_URL = 'http://100.31.94.19:8081/artifactory'
        ARTIFACTORY_REPO = 'libs-release-local'
        // SONAR_HOST_URL = 'http://18.209.9.215:9000/'
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

        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv('sonarqube') {
        //             withCredentials([string(credentialsId: 'sonar-jenkins-token', variable: 'SONAR_TOKEN')]) {
        //                 sh """
        //                 mvn sonar:sonar \
        //                     -Dsonar.projectKey=reports \
        //                     -Dsonar.projectName=reports \
        //                     -Dsonar.host.url=$SONAR_HOST_URL \
        //                     -Dsonar.login=$SONAR_TOKEN \
        //                     -Dsonar.java.binaries=target/classes
        //                 """
        //             }
        //         }
        //     }
        // }

        // stage('Quality Gate Check') {
        //     steps {
        //         timeout(time: 5, unit: 'MINUTES') {
        //             waitForQualityGate abortPipeline: true
        //         }
        //     }
        // }

        stage('Upload Artifact to JFrog') {
            steps {
                withCredentials([string(credentialsId: 'jfrog-creds', variable: 'JFROG_TOKEN')]) {
                    sh '''
                        WAR_PATH=$(find target -name "*.war" | head -n 1)
                        FILE_NAME=$(basename "$WAR_PATH")
                        VERSION=$(grep -m1 "<version>" pom.xml | sed -e "s/.*<version>\\(.*\\)<\\/version>.*/\\1/")

                        GROUP_PATH="koddas/web/war/wwp/$VERSION"

                        echo "Uploading to JFrog: $FILE_NAME"
                        echo "Version: $VERSION"
                        echo "Repo Path: $GROUP_PATH"

                        curl -H "X-JFrog-Art-Api:$JFROG_TOKEN" \
                        -T "$WAR_PATH" \
                        "$ARTIFACTORY_URL/$ARTIFACTORY_REPO/$GROUP_PATH/$FILE_NAME"
                    '''
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

                        curl -u "$TOMCAT_USER:$TOMCAT_PASS" \
                        -T "$WAR_PATH" \
                        "http://52.3.183.141:8080/manager/text/deploy?path=/Rest-API&update=true"
                    '''
                }
            }
        }
    }
}
