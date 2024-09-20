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
                            
                            sshCommand remote: remote, command: "ls -lrt | pwd"
                            sshPut remote: remote, from: 'target/LoginWebApp.war', into: '.'
                            sshPut remote: remote, from: 'Dockerfile', into: '.'
                            sshCommand remote: remote, command: "sudo docker build -t tomcat ."
                            sshCommand remote: remote, command: "sudo docker image ls -a"
                            sshCommand remote: remote, command: "sudo docker run --name tomcat-c -d -p 8080:8080 tomcat"
                            // sshCommand remote: remote, command: "sudo chown $USER:$USER /var/run/docker.sock"
                            sshCommand remote: remote, command: "sudo docker container ls -a"
                            sshCommand remote: remote, command: "ls -lrt"
                    }
                }
            }
        }
    }
}
