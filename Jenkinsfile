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
	sh 'gitleaks_results.csv || true'
        sh 'docker run opendevsecops/gitleaks --repo=https://github.com/maneeshas2018/devsecops-demo.git --report=gitleaks_results.csv' 
        sh 'cat gitleaks_results.csv'
      }
    } 
	  
   stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/maneeshas2018/devsecops-demo/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.csv'
        
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
