pipeline {
  agent any

  tools {
    maven 'Maven'     // Make sure this is configured in Jenkins
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
        dir('VulnerableApp') {  // Replace with actual subfolder if needed
          sh 'mvn clean install -DskipTests'
        }
      }
    }

    stage('SCA: OWASP Dependency-Check') {
      steps {
        dir('VulnerableApp') {
          dependencyCheck additionalArguments: '--format XML --format HTML --scan .', odcInstallation: 'ODC'
        }
      }
    }

    stage('Archive SCA Report') {
      steps {
        archiveArtifacts artifacts: '**/dependency-check-report.xml', fingerprint: true
        sh 'pwd && ls -la'
      }
    }
  }

  post {
    always {
      dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
      echo "Cleanup workspace..."
      deleteDir()
    }
    failure {
      echo "Pipeline failed."
    }
  }
}
