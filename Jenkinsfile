pipeline {
    agent any

    stages {
        stage('Build Artifact - Maven') {
            steps {
                sh "mvn clean package -DskipTests=true"
                archive 'target/*.jar'
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
        stage('SonarQube - SAST') {
            steps {
                sh "mvn sonar:sonar \
        -Dsonar.projectKey=numeric-application  -Dsonar.host.url=http://devsecops-demo-nyc.eastus.cloudapp.azure.com:9000 -Dsonar.login=e354ae001974827a1a8fc325efa5909df654c424"
            }
        }
        stage('Docker Build and Push') {
            steps {
                withDockerRegistry([credentialsId: "docker", url: ""]) {
                    sh 'printenv'
                    sh "docker build -t gvindio/numeric-app:${GIT_COMMIT} ."
                    sh "docker push gvindio/numeric-app:${GIT_COMMIT}"
                }
            }
        }
      stage('Kubernetes Deployment - DEV') {
          steps {
              withKubeConfig([credentialsId: 'kubeconfig']) {
              sh "sed -i 's#replace#gvindio/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              sh "kubectl apply -f k8s_deployment_service.yaml"
        }
      }
    }
    
  }

}

