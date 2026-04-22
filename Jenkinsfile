pipeline {
    agent any
    stages {
      stage('git clone backend') {
          steps {
            git branch: 'phase4-pipeline', url: 'https://github.com/jasonantonacci1-ai/Frontend-repo.git'
          }
       }
       stage('Build Docker Image') {
           steps {
               sh 'docker build -t jasonantonacci1/frontend-app .'
          }
       }
       stage('Critical security scan') {
           steps {
               sh 'trivy image --severity CRITICAL --exit-code 1 frontend-app'
          } 
       }
       stage('Code quality scan') {
           steps {
               withSonarQubeEnv('sonarqube') {
                   sh 'sonar-scanner'
               }
           timeout(time: 5, unit: 'MINUTES') {
               waitForQualityGate abotePipeline: true
               }
           }
       }
       stage('Push Image') {
           steps {
               sh 'docker push jasonantonacci1/frontend-app'
           }
       }
       stage('Trigger Config Pipeline') {
           steps {
               sh 'curl -X POST http://localhost:8080/generic-webhook-trigger/invoke?token=your-trigger-token'
           }
       }
    }
}
