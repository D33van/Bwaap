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
       sh 'docker run gesellix/trufflehog --json https://github.com/D33van/Bwaap.git > trufflehog'
        sh 'cat trufflehog'
     }
    }
    
  //stage ('Source Composition Analysis') {
    //steps {
      //   sh 'rm owasp* || true'
        // sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         //sh 'chmod -R 777 owasp-dependency-check.sh'
         //sh 'bash owasp-dependency-check.sh'
         //sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      //}
    //}
    
    stage ('JAVA') {
    steps{
    sh 'java -version' 
    }
  }
    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar -e -X'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/GITpipe/target/WebApp.war justdial@172.29.87.55:/opt/tomcat/webapps/webapp.war'
              }      
           }       
    }
    
    
    stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no justdial@172.29.87.55 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://172.29.87.55:8888/webapp/" || true'
        }
      }
    }
    
  }
}
