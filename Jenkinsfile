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
        sh 'rm trufflehog || true'
        sh 'docker run  mani2020/tufflehog-sec-demo:latest --json https://github.com/maneeshas2018/devsecops-demo.git > trufflehog'
        sh 'cat trufflehog'
      }
    } 
	  
   stage ('OWASP Dependency-Check Vulnerabilities') {  
    steps {   
      sh 'mvn dependency-check:check'  
     dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'  
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
