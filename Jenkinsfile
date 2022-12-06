pipeline {
   agent any

   environment {

    ARTIFACTORY_URL="http://172.31.32.31:8082/artifactory"
    ARTIFACTORY_ID="artifactory-1"
    ARTIFACTORY_CREDS="jfrog-creds"

   }
   stages{

    stage("Build Code"){
      steps {
         script {
            sh '''
            mvn install
            myver="\$(grep -i version pom.xml |head -2 |tail -1|cut -d">" -f2|cut -d"<" -f1)"
            mv target/*.war target/myApp-v.\$myver-release-\${BUILD_NUMBER}.war
            '''


         }
      }
    }

    stage("runing Unit Tests"){
      steps {
         script {
            sh "mvn test"
         }
      }
    }
    stage("Code Analysis"){
      environment {
            def sonarHome = tool name: 'sonarscanner-4.7'
      }
      steps {  
            withSonarQubeEnv('mysonarserver') {
               sh "${sonarHome}/bin/sonar-scanner -Dproject.settings=./myjavaapp.properties" 
            }
            sleep time: 30000, unit: 'MILLISECONDS'
            script {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
            }

      }
   }

    stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "${ARTIFACTORY_ID}",
                    url: "${ARTIFACTORY_URL}",
                    credentialsId: "${ARTIFACTORY_CREDS}"
                )
            }
    }
    stage ('Upload Artifacts') {
            steps {
               rtUpload (
                    // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
                    serverId: "artifactory-1",
                    spec: """{
                            "files": [
                                    {
                                        "pattern": "target/*.war",
                                        "target": "example-repo-local/vinod/"
                                    }
                                ]
                            }"""
               )
            }
        }

   }
   post {
      always {
         cleanWs()
      }
   }
}
