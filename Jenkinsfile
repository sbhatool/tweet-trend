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
                echo "-------- build started ------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "-------- build completed ------"
            }
        }
        stage("test"){
            steps{
                echo "-------- unit test started ------"
                sh 'mvn surefire-report:report'
                echo "-------- unit test completed ------"
            }
        }

        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv(installationName: 'sbhatool-sonarqube-server') {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.11.0.3922:sonar'
            }
            
            }
        }
    }
}