pipeline {
  agent any

  tools {
    gradle 'Gradle'     // Must be configured in Jenkins Global Tool Configuration
    jdk 'OpenJDK'       // Java 11 or 17
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/rueben-hytech/VulnerableApp.git', branch: 'master'
      }
    }

    stage('Build with Gradle') {
      steps {
        dir('DepencyCheck') {
          // Use the wrapper if present, fallback to Jenkins' Gradle
          sh './gradlew clean build -x test || gradle clean build -x test'
        }
      }
    }

    stage('SCA: OWASP Dependency-Check') {
      steps {
        dir('DepencyCheck') {
          dependencyCheck additionalArguments: '--format XML --format HTML --scan .', odcInstallation: 'ODC'
        }
      }
    }

    stage('Archive Reports') {
      steps {
        archiveArtifacts artifacts: 'DepencyCheck/**/dependency-check-report.*', fingerprint: true
        sh 'echo "Files in DepencyCheck folder:" && ls -la DepencyCheck'
      }
    }
  }

  post {
    always {
      dependencyCheckPublisher pattern: 'DepencyCheck/**/dependency-check-report.xml'
      echo "Cleaning up workspace..."
      deleteDir()
    }
    failure {
      echo "❌ Pipeline failed"
    }
  }
}
