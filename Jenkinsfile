pipeline {
    agent any

    tools {
        maven 'M2_HOME'
    }

    stages {
        stage('checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Lipughadei/LoginWebApp.git'
            }
        }

        stage('build') {
            steps {
                sh 'whoami'
                sh 'mvn clean package'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.war', followSymlinks: false
                }
            }
        }

        stage('upload to s3 bucket') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-credential', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh 'aws s3 ls'
                    sh 'aws s3 cp target/*.war s3://bucket-war/artifactory/'
                }
            }
        }

        stage('upload through the s3 upload plugin') {
            steps {
                script {
                    s3Upload(
                        consoleLogLevel: 'INFO',
                        dontSetBuildResultOnFailure: false,
                        dontWaitForConcurrentBuildCompletion: false,
                        entries: [[
                            bucket: 'bucket-war/artifactory',
                            excludedFile: '',
                            flatten: false,
                            gzipFiles: false,
                            keepForever: false,
                            managedArtifacts: false,
                            noUploadOnFailure: false,
                            selectedRegion: 'ap-south-1',
                            showDirectlyInBrowser: false,
                            sourceFile: 'target/*.war',
                            storageClass: 'STANDARD',
                            uploadFromSlave: false,
                            useServerSideEncryption: false
                        ]],
                        pluginFailureResultConstraint: 'FAILURE',
                        profileName: 's3war',
                        userMetadata: []
                    )
                }
            }
        }

        stage('building the image and pushing it to the dockerhub') {
            agent {
                label 'agent1'
            }
            steps {
                script {
                    withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-credential', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh 'aws --version'
                        // sh 'aws s3 cp s3://bucket-war/artifactory/ /home/ec2-user' //defected item
                        //  sh 'aws s3 cp s3://bucket-war/artifactory/LoginWebApp.war /home/ec2-user/jenkins/workspace/pipeline_job'
                        sh 'aws s3 cp s3://bucket-war/artifactory/LoginWebApp.war .'
                        // sh 'mv /home/ec2-user/jenkins/workspace/pipeline_job/LoginWebApp.war /home/ec2-user'
                        // sh 'sudo docker ps'
                        sh 'sudo docker image prune -a -f'
                        sh 'pwd'
                        sh 'ls -la'
                        // sh 'sudo docker build -t lipughadei/tomcat:v1.0 -f Dockerfile-tomcat /home/ec2-user/jenkins/workspace/pipeline_job/Docker'
                        // sh 'sudo docker build -t lipughadei/mysql:v1.0 -f Dockerfile-mysql /home/ec2-user/jenkins/workspace/pipeline_job/Docker'
                        sh 'sudo docker build -t lipughadei/tomcat:v1.0 -f Docker/Dockerfile-tomcat .'
                        sh 'sudo docker build -t lipughadei/mysql:v1.0 -f Docker/Dockerfile-mysql .'
                        // sh 'sudo docker tag tomcat:1.0 lipughadei/tomcat:v1.0'
                        // sh 'sudo docker tag mysql:1.0 lipughadei/mysql:v1.0'
                        // sh 'docker login -u “lipughadei” -p “Dockerhammer1@” docker.io' ///it is a insecure way
                        sh 'sudo docker login -u "lipughadei" --password Dockerhammer1@ docker.io'
                        // sh 'echo "Dockerhammer1@" | docker login -u "lipughadei" --password-stdin docker.io'
                        sh 'sudo docker push lipughadei/tomcat:v1.0'
                        sh 'sudo docker push lipughadei/mysql:v1.0'
                        // sh 'sudo docker-compose down'
                    }
                }
            }
        }
        stage('ansible'){
            agent {
                label 'agent2'
            }
            steps {
                sh 'pwd'
                sh 'ls -la'
                // sh 'sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose'
                sh 'cd ansible1/'
                //sh 'sudo ansible-playbook ansible1/implement1.yml' //if you connected your jenkins agent through root
                sh 'sudo ansible-playbook ansible1/implement1.yml' // if you connected your jenkins agent through ec2-user
            }
        }
        // stage('deployment') {
        //     steps {
        //         script {
        //             withCredentials([sshUserPrivateKey(credentialsId: 'container', usernameVariable: 'USERNAME', keyFileVariable: 'keyFile')]) {
        //                 def remote = [:]
        //                 remote.name = 'container'
        //                 remote.host = '3.109.153.173'
        //                 remote.allowAnyHosts = true
        //                 remote.user = username
        //                 remote.identityFile = keyFile  
        //                 sshCommand remote: remote, command: "ls -lrt | pwd"
        //                 sshPut remote: remote, from: 'target/LoginWebApp.war', into: '.'
        //                 sshPut remote: remote, from: 'Dockerfile', into: '.'
        //                 sshCommand remote: remote, command: "sudo docker image rm -f tomcat"
        //                 sshCommand remote: remote, command: "sudo docker build -t tomcat ."
        //                 sshCommand remote: remote, command: "sudo docker image ls -a"
        //                 sshCommand remote: remote, command: "sudo docker container stop tomcat-c"
        //                 sshCommand remote: remote, command: "sudo docker container rm tomcat-c"
        //                 sshCommand remote: remote, command: "sudo docker run --name tomcat-c -d -p 8080:8080 tomcat"
        //                 sshCommand remote: remote, command: "sudo docker container stop tomcat-c"
        //                 sshCommand remote: remote, command: "sudo chown $USER:$USER /var/run/docker.sock"
        //                 sshCommand remote: remote, command: "sudo docker container ls -a"
        //                 sshCommand remote: remote, command: "ls -lrt"
        //             }
        //         }
        //     }
        // }

        stage('clean Workspace') {
            steps {
                cleanWs()
                echo "clean ws"
            }
        }
    }
}