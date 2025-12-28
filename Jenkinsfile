pipeline {
    agent any

    parameters {
        string(name: 'APP_VERSION', defaultValue: '1.0.0', description: 'Enter application version')
        choice(name: 'ENVIRONMENT', choices: ['DEV', 'QA', 'PROD'], description: 'Select deployment environment')
        booleanParam(name: 'UPLOAD_ARTIFACT', defaultValue: true, description: 'Upload Artifact to JFrog?')
    }

    environment {
        ARTIFACTORY_URL = 'http://100.31.94.19:8081/artifactory'
        ARTIFACTORY_REPO = 'libs-release-local'
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

        stage('Upload Artifact to JFrog') {
            when { expression { return params.UPLOAD_ARTIFACT } }
            steps {
                withCredentials([string(credentialsId: 'jfrog-creds', variable: 'JFROG_TOKEN')]) {
                    sh '''
                        WAR_PATH=$(find target -name "*.war" | head -n 1)
                        FILE_NAME=$(basename "$WAR_PATH")

                        GROUP_PATH="koddas/web/war/wwp/${APP_VERSION}"

                        echo "Uploading to JFrog: $FILE_NAME"
                        echo "Version: $APP_VERSION"
                        echo "Deploy Env: ${ENVIRONMENT}"
                        echo "Repo Path: $GROUP_PATH"

                        curl -H "X-JFrog-Art-Api:$JFROG_TOKEN" \
                        -T "$WAR_PATH" \
                        "$ARTIFACTORY_URL/$ARTIFACTORY_REPO/$GROUP_PATH/$FILE_NAME"
                    '''
                }
            }
        }
    }
}
