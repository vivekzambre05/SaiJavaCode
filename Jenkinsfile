pipeline {
  agent any

  // Make sure this matches the "Name" you gave your Maven install in Jenkins
  tools {
    maven 'Maven3'
  }

  stages {
    stage('Checkout') {
      steps {
        // Uses the Multibranch SCM settings or configured credentials
        checkout scm
      }
    }

    stage('Build & Test') {
      steps {
        // You can adjust memory flags if needed
        withEnv(["MAVEN_OPTS=-Xmx512m"]) {
          sh 'mvn clean verify'
        }
      }
    }

    stage('SonarQube Analysis') {
      steps {
        // 'SonarQube' must match the name in Manage Jenkins → Configure System → SonarQube Servers
        withSonarQubeEnv('SonarQube') {
          sh """
            mvn sonar:sonar \
              -Dsonar.projectKey=SaiJavaCode \
              -Dsonar.host.url=$SONAR_HOST_URL \
              -Dsonar.login=$SONAR_AUTH_TOKEN
          """
        }
      }
    }

    stage('Quality Gate') {
      steps {
        // This will abort the pipeline if the SonarQube quality gate fails
        waitForQualityGate abortPipeline: true
      }
    }
  }

  post {
    success {
      echo '✅ Pipeline completed successfully!'
    }
    failure {
      echo '❌ Pipeline failed. Check console output and SonarQube dashboard for details.'
    }
  }
}
