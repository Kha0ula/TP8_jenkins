pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        archiveArtifacts 'build/libs/*.jar'
        archiveArtifacts 'build/docs/javadoc/**'
        junit 'build/reports/**'
      }
    }

    stage('Mail Notification') {
      steps {
        mail(subject: 'TP Jenkins', body: 'build done', to: 'ik_gribissa@esi.dz')
      }
    }

    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            sh 'gradle sonarqube'
            waitForQualityGate true
          }
        }

        stage('Test reporting') {
          steps {
            cucumber 'reports/report.html'
          }
        }

      }
    }

    stage('Deployment') {
      steps {
        sh '''publishing {
    repositories {
        maven {
            url System.getenv("url")
            credentials {
                username System.getenv("username")
                password System.getenv("password")
            }
        }
    }

    publications {
        maven(MavenPublication) {
            from components.java
        }
    }
}'''
        }
      }

      stage('Slack Notification') {
        steps {
          slackSend(baseUrl: 'https://hooks.slack.com/services/', token: 'T034EPHRMT9/B035CMR7EKT/45TsUK6RxKhg7q7J0AwXXQTJ', message: 'déploiement terminée!')
        }
      }

    }
  }