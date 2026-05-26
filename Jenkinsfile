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
               dir('frontend') {
               sh 'docker build -t jasonantonacci1/frontend-app .'
             }
          }
       }
       stage('Critical security scan') {
           steps {
               sh 'trivy image --severity CRITICAL --exit-code 1 jasonantonacci1/frontend-app --format json --output trivy-report.json'
               archiveArtifacts artifacts: 'trivy-report.json'
          } 
       }
       stage('Code quality scan') {
            steps {
                dir('frontend') {
                    withSonarQubeEnv('sonarqube') {
                        sh /opt/sonar-scanner-5.0.1.3006-linux/bin/sonar-scanner -Dsonar.projectKey=frontend-repo'
                    }
                }
            timeout(time: 5, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
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
    post {
        failure {
            mail to: 'jasonantonacci2@gmail.com',
                 subject: 'Pipleine Failure Report',
                 body: "The frontend pipeline has failed. Please check the logs here: ${env.BUILD_URL}"
        }
    }
}
