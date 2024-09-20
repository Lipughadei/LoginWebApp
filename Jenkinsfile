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
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'container', usernameVariable: 'USERNAME', keyFileVariable: 'keyFile')]) {
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
}
