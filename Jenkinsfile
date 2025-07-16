pipeline {
  agent any

  tools {
    maven 'Maven'     // Make sure Maven is set up in Jenkins global config
    jdk 'OpenJDK'     // Java 11 or 17
  }

  stages {

    stage('Checkout') {
      steps {
        git url: 'https://github.com/rueben-hytech/VulnerableApp.git', branch: 'master'
      }
    }

    stage('Build with Maven') {
      steps {
        dir('DepencyCheck') {
          sh 'mvn clean install -DskipTests'
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

    stage('Archive SCA Report') {
      steps {
        archiveArtifacts artifacts: 'DepencyCheck/**/dependency-check-report.*', fingerprint: true
        sh 'echo "Listing DepencyCheck report folder:" && ls -la DepencyCheck/'
      }
    }
  }

  post {
    always {
      dependencyCheckPublisher pattern: 'DepencyCheck/**/dependency-check-report.xml'
      echo "Cleanup workspace..."
      deleteDir()
    }
    failure {
      echo "Pipeline failed."
    }
  }
}
