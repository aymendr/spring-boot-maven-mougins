pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'master' , url: 'https://github.com/aymendr/spring-boot-maven-mougins.git'
            }
        }
        stage('Build Maven and archive') {
            steps {
                sh "mvn -DskipTests clean package"
                archive "target/*.jar"
               }
        }
        stage('SonarQube Analysis') {
            steps {
                // Execute SonarQube analysis
                script {
                    def scannerHome = tool 'sonarTool'
                    withSonarQubeEnv('sonar_server') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=demo \
                        -Dsonar.java.binaries=target/**"
                    }
                }
            }
        }
        stage("Nexus") {
        	steps {
        		nexusArtifactUploader artifacts:
        		[[
        			artifactId: 'demo',
        			classifier: '',
        			file: 'target/*.jar',
        			type: 'jar'
        		]],
        		credentialsId: 'nexus_pwd',
        		groupId: 'com.example',
        		nexusUrl: '192.168.56.102:8081',
        		nexusVersion: 'nexus3',
        		protocol: 'http',
        		repository: 'new-repo',
        		version: '1.0-SNAPSHOT'
        	}
        }

    }
}