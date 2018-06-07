pipeline {
  agent any
  stages {
    stage('checkout project') {
      steps {
        checkout scm
      }
    }
    stage('test') {
      steps {
        sh 'docker run -v `pwd`:/app -v $HOME/.m2:/root/.m2 -w /app localhost:5000/maven mvn cobertura:cobertura test'
      }
    }
    stage('report') {
      parallel {
        stage('report') {
          steps {
            junit 'target/surefire-reports/*.xml'
          }
        }
        stage('Coverage') {
          steps {
            cobertura(coberturaReportFile: 'target/site/cobertura/coverage.xml')
          }
        }
      }
    }
    stage('package') {
      steps {
        sh 'docker run -v `pwd`:/app -v  $HOME/.m2:/root/.m2 -w /app -p 8800:8000 localhost:5000/maven mvn package'
      }
    }
    stage('archive') {
      steps {
        archiveArtifacts 'target/*.jar'
      }
    }
    stage('wait for confirm') {
      input {
        message 'Should we deploy?'
        id 'Yes, we should.'
        submitter 'admin'
        parameters {
          string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
        }
      }
      steps {
        echo "Hello, ${PERSON}, nice to meet you."
      }
    }
    stage('deploy') {
      steps {
        sh 'make deploy-default'
      }
    }
  }
  post {
    always {
      echo 'I will always say Hello again!'

    }

    success {
      echo 'success!'

    }

    failure {
      echo 'failure!'

    }

  }
}