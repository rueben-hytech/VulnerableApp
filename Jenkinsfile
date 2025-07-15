pipeline {
  agent any

  tools {
    maven 'Maven_3' // Configure this in Jenkins Global Tool Configuration
    jdk 'JDK_17'    // Configure JDK in Jenkins (Java 17 or Java 11 is fine)
  }

  stages {

    stage('Checkout') {
      steps {
        git url: 'https://github.com/rueben-hytech/VulnerableApp.git', branch: 'main'
      }
    }

    stage('Build with Maven') {
      steps {
        sh 'mvn clean install -DskipTests'
      }
    }

    stage('SCA: OWASP Dependency-Check') {
      steps {
        sh '''
          wget https://github.com/jeremylong/DependencyCheck/releases/download/v8.4.0/dependency-check-8.4.0-release.zip
          unzip dependency-check-8.4.0-release.zip -d dc
          ./dc/dependency-check/bin/dependency-check.sh --project "VulnerableApp SCA" --scan . --format "HTML" --out reports
        '''
      }
    }

    stage('Archive SCA Report') {
      steps {
        archiveArtifacts artifacts: 'reports/**', fingerprint: true
      }
    }
  }

  post {
    always {
      echo "Cleanup workspace..."
      deleteDir()
    }
    failure {
      echo "Pipeline failed."
    }
  }
}
