def registry = 'https://sbhatool01.jfrog.io'
def imageName = 'sbhatool01.jfrog.io/sbhatool-docker-local/ttrend'
def version   = '2.1.3'
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
        
        stage("Jar Publish") {
            steps {
                script {
                        echo '<--------------- Jar Publish Started --------------->'
                        def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
                        def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                        def uploadSpec = """{
                            "files": [
                                {
                                "pattern": "jarstaging/(*)",
                                "target": "mavenrepo-libs-release-local/{1}",
                                "flat": "false",
                                "props" : "${properties}",
                                "exclusions": [ "*.sha1", "*.md5"]
                                }
                            ]
                        }"""
                        def buildInfo = server.upload(uploadSpec)
                        buildInfo.env.collect()
                        server.publishBuildInfo(buildInfo)
                        echo '<--------------- Jar Publish Ended --------------->'  
                }
            }   
        }


        stage(" Docker Build ") {
            steps {
               script {
                        echo '<--------------- Docker Build Started --------------->'
                        app = docker.build(imageName+":"+version)
                        echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

         stage (" Docker Publish "){
            steps {
             script {
                        echo '<--------------- Docker Publish Started --------------->'  
                         docker.withRegistry(registry, 'artifact-cred'){
                         app.push()
                }    
                        echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }

stage(" Deploy ") {
       steps {
         script {
            echo '<--------------- Helm Deploy Started --------------->'
            sh 'helm install ttrend ttrend-0.1.0.tgz'
            echo '<--------------- Helm deploy Ends --------------->'
         }
       }
     }

}
}