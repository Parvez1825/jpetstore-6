pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                  branches: [[name: '*/release']],
                  userRemoteConfigs: [[url: 'https://github.com/Parvez1825/jpetstore-6.git']]
                ])
            }
        }
        stage('Build') {
            steps {
                script {
                    if (isUnix()) {
                        sh './mvnw clean package'
                    } else {
                        bat '.\\mvnw.cmd clean package'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sshPublisher(
                  publishers: [
                    sshPublisherDesc(
                      configName: 'tomcat-server',
                      transfers: [
                        sshTransfer(
                          sourceFiles: 'target/*.war',
                          remoteDirectory: 'webapps',
                          removePrefix: 'target'
                        )
                      ],
                      verbose: true
                    )
                  ]
                )
            }
        }
    }
}
