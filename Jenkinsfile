pipeline{
  agent {
    docker {
      image 'maven:3-alpine'
      args '-v /root/.m2:/root/.m2'
    }
  }
  stages {
    stage('Build'){
      steps{
        sh 'mvn -B -DskipTests clean package'
      }
     }
    stage('Test'){
      steps{
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
              withSonarQubeEnv('simplemaven') {
                 sh 'java -version'
                 sh 'mvn clean package sonar:sonar'
              }
            }
          }
       stage("Quality gate") {
            steps {
               waitForQualityGate abortPipeline: true
            }
        }
   }
