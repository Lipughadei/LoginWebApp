pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    stages {
        stage ('build') {
            steps {
                git branch: 'master',
                url: 'https://github.com/Lipughadei/LoginWebApp.git'
            }
        }
    }
}