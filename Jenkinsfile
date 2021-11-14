pipeline {
  agent {
    kubernetes {
      yamlFile 'build-agent.yaml'
      defaultContainer 'gradle'
      idleMinutes 1
    }
  }
  stages {
    stage('Setup') {
      parallel {
        stage('Install Dependencies') {
          steps {
            container('gradle') {
              sh './gradlew dependencies'
            }
          }
        }
        stage('Secrets scanner') {
          steps {
            container('trufflehog') {
              sh 'git clone ${GIT_URL}'
              sh 'cd sample-api-service && ls -al'
              sh 'cd sample-api-service && trufflehog .'
              sh 'rm -rf sample-api-service'
            }
          }
        }
      }
    }
    stage('Build') {
      steps {
        container('gradle') {
          sh './gradlew build'
        }
      }
    }
    stage('Static Analysis') {
      parallel {
        stage('Unit Tests') {
          steps {
            container('gradle') {
              sh './gradlew test'
            }
          }
        }
      }
    }
  }
}