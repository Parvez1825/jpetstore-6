pipeline {
    agent any
    
    stages {
        stage('Build WAR') {
            steps {
                echo "Building WAR file..."
                sh 'mvn clean package -DskipTests'
                archiveArtifacts 'target/*.war'
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying to Tomcat server..."
                sshagent(['tomcat-key']) {
                    sh '''
                    echo "Copying WAR file to server..."
                    scp -o StrictHostKeyChecking=no target/jpetstore.war ubuntu@3.111.173.229:/tmp/
                    
                    echo "Deploying on Tomcat server..."
                    ssh -o StrictHostKeyChecking=no ubuntu@3.111.173.229 "
                        echo 'Stopping Tomcat...'
                        sudo /opt/tomcat/tomcat9/bin/shutdown.sh || true
                        sleep 5
                        
                        echo 'Removing old app...'
                        sudo rm -rf /opt/tomcat/tomcat9/webapps/jpetstore*
                        
                        echo 'Copying new WAR...'
                        sudo cp /tmp/jpetstore.war /opt/tomcat/tomcat9/webapps/
                        sudo chown tomcat:tomcat /opt/tomcat/tomcat9/webapps/jpetstore.war
                        
                        echo 'Starting Tomcat...'
                        sudo /opt/tomcat/tomcat9/bin/startup.sh
                        
                        echo 'Deployment completed!'
                    "
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo "✅ Deployment successful! 🚀"
            echo "Application URL: http://3.111.173.229:8080/jpetstore"
        }
        failure {
            echo "❌ Deployment failed! Check logs."
        }
    }
}
