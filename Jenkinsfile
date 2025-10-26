pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build (fat jar)') {
      steps {
        // make the Maven wrapper executable in Linux
        sh 'chmod +x mvnw'
        sh './mvnw -q -DskipTests clean package'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }
    stage('Smoke (optional)') {
      steps {
        sh '''
          JAR=$(ls target/*SNAPSHOT.jar | head -n1)
          nohup java -jar "$JAR" > app.log 2>&1 &
          sleep 5
          curl -I http://localhost:8080 || true
          pkill -f "$JAR" || true
        '''
      }
    }
  }
}
pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build Jar') {
      steps {
        bat 'mvnw.cmd -DskipTests clean package'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }
  }
}