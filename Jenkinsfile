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
                        sh '''
                            aws --version
                            aws s3 cp s3://bucket-war/artifactory/LoginWebApp.war .
                            sudo docker image prune -a -f
                            pwd
                            ls -la
                            sudo docker build -t lipughadei/tomcat:v1.0 -f Docker/Dockerfile-tomcat .
                            sudo docker build -t lipughadei/mysql:v1.0 -f Docker/Dockerfile-mysql .
                            sudo docker login -u "lipughadei" --password Dockerhammer1@ docker.io
                            sudo docker push lipughadei/tomcat:v1.0
                            sudo docker push lipughadei/mysql:v1.0
                        '''
                        cleanWs()
                        echo 'clean workspace'
                    }
                }
            }
        }
        stage('ansible'){
            agent {
                label 'agent2'
            }
            steps {
                sh '''
                pwd
                ls -la
                cd ansible1/ && sudo ansible-playbook implement1.yml
                '''
                cleanWs()
                echo 'clean workspace'
            }
        }
        stage('clean Workspace') {
            steps {
                cleanWs()
                echo "clean ws"
            }
        }
    }
}