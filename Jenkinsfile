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
        stage ('build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('deployment') {
            steps {
                //deploy adapters: [tomcat9(credentialsId: 'lipu', path: '', url: 'http://13.201.19.54:8080/')], contextPath: null, war: '**/*.war'
                withCredentials([sshUserPrivateKey(credentialsId: 'container', keyFileVariable: 'keyFile', usernameVariable: 'username')]) {
                echo 'this is now done'
                def remote = [:]
                remote.name = 'container'
                remote.host = '3.109.153.173'
                remote.allowAnyHosts = true
                remote.user = username
                remote.identityFile = keyFile

                sshCommand remote: remote, command: "ls -lrt"
                }
            }
        }
    }
}
