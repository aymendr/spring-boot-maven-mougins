pipeline {
    agent any

    stages {
        stage('Check java version') {
            steps {
                sh "java -version"
                sh "env | grep -e PATH -e JAVA_HOME"
                sh "mvn -version"
               }
        }
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
        /*stage('Deploy on nexus machine') {
            agent { label 'nexus'}
            steps {
                sh "touch file"
               }
        }*/
        
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

        stage("Nexus"){
           steps {
            nexusArtifactUploader artifacts: 
            [[artifactId: 'demo', 
            classifier: '', 
            file: 'target/demo-1.0-SNAPSHOT.jar', 
            type: 'jar'
            ]], 
            credentialsId: 'nexus_pwd', 
            groupId: 'com.example', 
            nexusUrl: '192.168.56.102:8081', 
            nexusVersion: 'nexus3', 
            protocol: 'http', 
            repository: 'release-mougins', 
            version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}"
           } 
        }
/*
        stage("Nexus") {
        	steps {
        		nexusArtifactUploader artifacts:
        		[[
        			artifactId: 'demo',
        			classifier: '',
        			file: 'target/demo-1.0-SNAPSHOT.jar',
        			type: 'jar'
        		]],
        		credentialsId: 'nexus_pwd',
        		groupId: 'com.example',
        		nexusUrl: '192.168.56.102:8081',
        		nexusVersion: 'nexus3',
        		protocol: 'http',
        		repository: 'new-repo',
        		version: '${env.BUILD_ID}-${env.BUILD_TIMESTAMP}'
        	}
        }
*/



    }
}