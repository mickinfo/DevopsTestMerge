pipeline {
    agent any 

    tools { 
        maven 'mavenjenkins'
        jdk 'jenkinsjava'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mickinfo/DevopsTestMerge.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }

        stage('Sonar Scanner') {
            steps {
                script {
                    def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
                        // Asegúrate de que las rutas a las fuentes y pruebas sean correctas
                        sh "${sonarqubeScannerHome}/bin/sonar-scanner -Dsonar.host.url=http://SonarQube:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=gs-gradle -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=src/main/java -Dsonar.tests=src/test/java -Dsonar.language=java -Dsonar.java.binaries=target/classes"
                    }
                }
            }
        }

        stage('Nexus Upload') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'nexus:8081',
                    groupId: 'com.kibernumacademy',
                    version: '0.0.1',
                    repository: 'devops-kiber',
                    credentialsId: 'NexusLogin',
                    artifacts: [
                        [artifactId: 'devops',
                        classifier: '',
                        file: 'target/devops-0.0.1.jar',
                        type: 'jar'],
                        [artifactId: 'devops',
                        classifier: '',
                        file: 'pom.xml',
                        type: 'pom']
                    ]
                )
            }
        }
    }
}
