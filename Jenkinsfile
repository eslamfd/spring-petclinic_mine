pipeline {
    agent any

    tools {
        maven 'Maven3' 
        jdk 'jdk17'    
    }

    environment {
        SONARQUBE = credentials('sonar-token') // Jenkins credential with Sonar token
    }

    stages {
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=petclinic'
                }
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build and test successful. JAR archived.'
        }
        failure {
            echo 'Something exploded. Logs will haunt you in Console Output.'
        }
    }
}
