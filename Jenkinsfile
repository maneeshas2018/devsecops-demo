pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
   stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
   stage ('Deploy') {
      steps {
      sshagent(['tomcat']) {
            sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@13.233.194.238:/home/ec2-user/prod/apache-tomcat-8.5.76/webapps'
            }      
           }       
    }
  }
}
