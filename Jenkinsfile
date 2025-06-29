pipeline {
  agent any
  tools { maven 'Maven' }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build & Test') {
      steps { sh 'mvn clean verify' }
    }
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh '''
            mvn sonar:sonar \
              -Dsonar.projectKey=SaiJavaCode \
              -Dsonar.host.url=$SONAR_HOST_URL \
              -Dsonar.login=$SONAR_AUTH_TOKEN
          '''
        }
      }
    }
    stage('Quality Gate') {
      steps { waitForQualityGate abortPipeline: true }
    }
  }
}
