pipeline{
   agent {
    docker {
      image 'maven:3.6.3-jdk-11'
      args '-v /root/.m2:/root/.m2'
    }
  }
  stages {
      stage("Maven Build"){
          steps{
             script{
                last_started=env.STAGE_NAME
               }
              sh 'mvn -B -DskipTests clean package'
             
          }
        
      }
   stage('Maven Test'){
            steps{
               script{
                  last_started=env.STAGE_NAME
            }
                sh 'mvn test'
            }
            post{
            always{
                junit 'target/surefire-reports/*.xml'
            }
        }
        }
         stage("Build & SonarQube analysis") {
            agent any
            steps {
               script{
                    last_started=env.STAGE_NAME
            }
              withSonarQubeEnv('simplemaven') {
                 
                sh 'java -version'
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
       stage("Quality gate") {
            steps {
               script{
                  last_started=env.STAGE_NAME
            }
                waitForQualityGate abortPipeline: true
            }
        }
     
     stage('Deploy to artifactory'){
        steps{
           script{
              last_started=env.STAGE_NAME
            }
        rtUpload(
         serverId : 'Art-server',
         spec :'''{
           "files" :[
           {
           "pattern":"target/*.jar",
           "target":"Art-simplemaven-2021"
           }
           ]
         }''', 
           
      )
      }
     }
    }
}
