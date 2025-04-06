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
            steps {
                withSonarQubeEnv(credentialsId: 'd76101e577c5fb9f9ac096861cbada508e108c7c', installationName: 'sbhatool-sonarqube-server') {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
            }
            
            }
        }

        
    }
}