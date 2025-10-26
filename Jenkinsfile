pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build (fat jar)') {
      steps {
        sh 'chmod +x mvnw'
        sh './mvnw -q -DskipTests clean package'
      }
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }
    stage('Smoke (8081)') {
      steps {
        sh '''
          JAR=$(ls target/*SNAPSHOT.jar | head -n1)
          nohup java -jar "$JAR" --server.port=8081 > app.log 2>&1 &
          sleep 6
          curl -I http://localhost:8081 || true
          pkill -f "$JAR" || true
        '''
      }
    }
  }
}
