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
        sh '''docker run -v `pwd`:/app -v  /home/user/.m2:/root/.m2 -w /app -p 8800:8000 localhost:5000/maven mvn package
'''
      }
    }
    stage('report') {
      parallel {
        stage('report') {
          steps {
            junit 'target/surefire-reports/*.xml'
          }
        }
        stage('coverage') {
          steps {
            cobertura(coberturaReportFile: 'target/site/cobertura/coverage.xml')
          }
        }
      }
    }
    stage('Package') {
      steps {
        sh 'docker run -v `pwd`:/app -v  /home/user/.m2:/root/.m2 -w /app -p 8800:8000 localhost:5000/maven mvn package'
        archiveArtifacts 'target/*.jar'
      }
    }
    stage('wait for comfirm') {
      input {
        message 'Should we deploy?'
        id 'Yes, we should.'
        submitter 'admin'
        parameters {
          string(name: 'PERSON', defaultValue: 'Mr. Jenkins', description: 'Who should I say hellp to?')
        }
      }
      steps {
        echo "Hello, ${PERSON},nice to meet you."
      }
    }
    stage('deploy') {
      steps {
        sh '''docker build -t localhost:5000/spring-boot-sample-prod ./

docker push localhost:5000/spring-boot-sample-prod

docker pull localhost:5000/spring-boot-sample-prod

docker run -d -p 8800:8000 localhost:5000/spring-boot-sample-prod'''
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