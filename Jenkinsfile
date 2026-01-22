pipeline {
  agent any

  // Ensure Jenkins knows which Node/npm to use
  tools {
    nodejs 'node'
  }
  environment {

    RECIPIENTS = 'methuthurai@gmail.com'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: ' https://github.com/MethuCS/8.2CDevSecOps.git'
      }
    }
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Run Tests') {
      steps {
        sh 'npm test || true' // Allows pipeline to continue despite test failures
      }
      post {
        always {
          emailext(
            subject: "Jenkins: Run Tests Stage - ${currentBuild.currentResult}",
            body: "Run Tests stage completed with status: ${currentBuild.currentResult}",
            recipientProviders: [
              [$class: 'DevelopersRecipientProvider']
            ],
            to: "${RECIPIENTS}",
            attachLog: true
          )
        }
      }
    }
    stage('Generate Coverage Report') {
      steps {
        // Ensure coverage report exists
        sh 'npm run coverage || true'
      }
    }
    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true' // This will show known CVEs in the output
      }
      post {
        always {
          emailext(
            subject: "Jenkins: NPM Audit Stage - ${currentBuild.currentResult}",
            body: "NPM Audit stage completed with status: ${currentBuild.currentResult}",
            recipientProviders: [
              [$class: 'DevelopersRecipientProvider']
            ],
            to: "${RECIPIENTS}",
            attachLog: true
          )
        }
      }

    }
  }
}