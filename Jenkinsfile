pipeline {
  agent any
  stages {
    stage('build') {
      post {
        failure {
          script {
            mail= " Build termine avec échec "
          }

        }

        success {
          script {
            mail=" Build termine avec succes "
          }

        }

      }
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        archiveArtifacts 'build/libs/*.jar'

        junit(testResults: 'build/test-results/test/*.xml', skipPublishingChecks: true, allowEmptyResults: true)
      }
    }

    stage('Mail Notification') {
      steps {
        mail(subject: 'Project JenkinsOGL notification', body: mail, cc: 'il_boukari@esi.dz' ,bcc:'is_bouzoul@esi.dz')
      }
    }

    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonar') {
              bat(script: 'gradle sonarqube', returnStatus: true)
            }

            waitForQualityGate true
          }
        }

        stage('Test Reporting') {
          steps {
            cucumber 'reports/*json'
          }
        }

      }
    }

    stage('Deployment') {
      steps {
        bat 'gradle publish'
      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(token: 'T02STNJ6V5F/B034T4Y5EBU/AXq8ifECAHzbiiZqKJXoGhmF', baseUrl: 'https://hooks.slack.com/services/', channel: '#general', message: 'notification for JenkinsOGL has been sent to slack')
      }
    }

  }
  environment {
    mail = ''
  }
}