pipeline {
  agent any

  tools {
    gradle 'Gradle'        // Make sure this matches Global Tool Config name
    jdk 'OpenJDK17'        // You must add JDK 17 in Global Tool Configuration
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
          sh 'gradle clean build -x test'
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
        archiveArtifacts artifacts: '**/dependency-check-report.xml', fingerprint: true
        sh 'pwd && ls -la'
      }
    }
  }

  post {
    always {
      dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
      echo 'Cleaning up workspace...'
      deleteDir()
    }
    failure {
      echo '❌ Pipeline failed'
    }
  }
}
