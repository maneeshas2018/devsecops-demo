pipeline {
  agent any 
  tools {
    maven 'Maven'
    dependency-check "Owasp"
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
      sh dependencyCheck additionalArguments: '-f "HTML, XML,CSV" -s .'   
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
            sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@15.206.89.235:/home/ec2-user/prod/apache-tomcat-8.5.76/webapps'
            }      
         }       
    }
	
    stage ('DAST') {
      steps {
        sshagent(['tomcat']) {
         sh 'ssh -o  StrictHostKeyChecking=no ec2-user@113.233.153.219 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://15.206.89.235:8080/webapp/" || true'
        }
      }
    } 
  }
}
