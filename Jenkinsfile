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
   stage ('Check-Git-Secrets') {
      steps {
        sh 'rm report_secretscan || true'
        sh 'docker run  gitguardian/ggshield --json https://github.com/maneeshas2018/devsecops-demo.git > report_secretscan'
        sh 'cat report_secretscan'
      }
    } 
	  
   
   stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
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
            sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@3.111.52.70:/home/ec2-user/prod/apache-tomcat-8.5.76/webapps'
            }      
         }       
    }
	
  }
}
