pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
}
    stages {
        stage("build"){
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('SonarQube analysis') {
            withSonarQubeEnv(credentialsId: 'd76101e577c5fb9f9ac096861cbada508e108c7c', installationName: 'sbhatool-sonarqube-server') { // You can override the credential to be used, If you have configured more than one global server connection, you can specify the corresponding SonarQube installation name configured in Jenkins
                sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
            }
        }

        
    }
}