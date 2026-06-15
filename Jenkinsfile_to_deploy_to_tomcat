pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Madhanavula/webapp.git'
            }
        }

        stage('Verify Tools') {
            steps {
                sh 'mvn -version'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonarqube') {
                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=webapp \
                    -Dsonar.projectName=webapp
                    '''
                }
            }
        }
        stage('Artifact uploader') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'my-webapp', classifier: '', file: 'target/my-webapp.war', type: 'war']], credentialsId: 'Nexus-Credentials', groupId: 'com.company', nexusUrl: '34.204.75.117:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'madhan-repo', version: '1.0-SNAPSHOT'
            }
        }
        
        stage('deploying to tomcat') {
            steps {
                sshagent(['Tomcat-server-agent']) {
                    sh 'scp -o StrictHostKeyChecking=no target/my-webapp.war ubuntu@3.94.204.179:/opt/tomcat/apache-tomcat-10.1.42/webapps'
                    
                }
            }
        }
        
    }
}
