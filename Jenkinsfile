pipeline {
   agent any

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

