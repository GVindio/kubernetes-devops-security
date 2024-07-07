pipeline {
    agent any

    tools {
        maven 'Maven'  // Ensure Maven is configured in Jenkins Global Tool Configuration
    }

    stages {
        stage('Build Artifact - Maven') {
            steps {
                sh "mvn clean package -DskipTests=true"
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
            }
        }

        stage('Unit Tests - JUnit and Jacoco') {
            steps {
                sh "mvn test"
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    jacoco execPattern: 'target/jacoco.exec'
                }
            }
        }
    }
}
