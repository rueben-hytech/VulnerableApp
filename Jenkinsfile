pipeline {
  agent any

  tools {
    maven 'Maven' // Configure this in Jenkins Global Tool Configuration
    jdk 'OpenJDK'    // Configure JDK in Jenkins (Java 17 or Java 11 is fine)
    Dependency-Check 'ODC' // Uses Dependency-Check 12.1.3
  }

  stages {

    stage('Checkout') {
      steps {
        git url: 'https://github.com/rueben-hytech/VulnerableApp.git', branch: 'master'
      }
    }

    stage('Build with Maven') {
      steps {
        sh 'mvn clean install -DskipTests'
      }
    }

    stage('SCA: OWASP Dependency-Check') {
      steps {
        dependencyCheck additionalArguments: '--format XML --format HTML --scan .', odcInstallation: 'ODC'
      }
    }

    stage('Archive SCA Report') {
      steps {
        archiveArtifacts artifacts: '**/dependency-check-report.xml', fingerprint: true
      }
    }
  }

  post {
    always {
      // This renders the interactive vulnerability results UI in Jenkins
      dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
      echo "Cleanup workspace..."
      deleteDir()
    }
    failure {
      echo "Pipeline failed."
    }
  }
}
