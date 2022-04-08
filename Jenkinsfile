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
        sh 'docker run  mani2020/tufflehog-sec-demo:latest --json https://github.com/maneeshas2018/devsecops-demo.git  > report_secretscan'
	sh 'cat report_secretscan '
	}
        
    } 
	  
   stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/maneeshas2018/devsecops-demo/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.json'
        
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
            sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@13.232.186.196:/home/ec2-user/prod/apache-tomcat-8.5.76/webapps'
            }      
         }       
    }
    stage ('DAST') {
      steps {
        sshagent(['tomcat']) {
         sh 'ssh -o  StrictHostKeyChecking=no ec2-user@13.232.5.124 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://13.232.186.196:8080/webapp/" || true'
        }
      }
    }
	
  }
}
