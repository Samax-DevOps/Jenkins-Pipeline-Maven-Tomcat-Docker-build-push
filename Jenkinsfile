pipeline{
    agent any
    tools{
        maven 'Maven'
    }

    stages{
        stage ('Build'){
            steps{
                sh 'mvn clean install'
            }
            post{
                success{
                    echo "archiving the artifacts"
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage ('Deploy'){
            steps{
                deploy adapters: [tomcat9(credentialsId: '50fe2c89-87a4-49a1-9fe6-0a2f1a1a8b46', path: '', url: 'http://192.168.1.153:8000/')], contextPath: null, war: '**/*.war'
            }
        }
    }
}
