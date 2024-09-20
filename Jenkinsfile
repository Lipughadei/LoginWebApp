pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    stages {
        stage ('checkout') {
            steps {
                git branch: 'master',
                url: 'https://github.com/Lipughadei/LoginWebApp.git'
            }
        }
        stage ('deployment') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'lipu', path: '', url: 'http://13.201.19.54:8080/')], contextPath: null, war: '**/*.war'
            }
        }
    }
}
