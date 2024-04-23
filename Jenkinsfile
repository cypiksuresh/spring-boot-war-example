pipeline {
    agent any
    
    // Define tools to be used in the pipeline
    tools {
        maven 'Maven' 
    }
    
    stages {
        stage("Test") {
            steps {
                // Run Maven test
                sh "mvn test"
                
                // Notify Slack channel about job start
                slackSend(channel: 'test', message: 'Job Started')
            }
        }
        
        stage("Build") {
            steps {
                // Build the Maven project
                sh "mvn package"
            }
        }
        
        stage("Deploy on Test") {
            steps {
                // Deploy to Test environment using the specified credentials and URL
                deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails1', path: '', url: 'http://35.225.37.137:8080')],
                       contextPath: '/app',
                       war: '**/*.war'
            }
        }
        
        stage("Deploy on Prod") {
            // Ask for manual input before deploying to Production
            input {
                message "Should we continue?"
                ok "Yes, we should"
            }
            
            steps {
                // Deploy to Production environment using the specified credentials and URL
                deploy adapters: [tomcat9(credentialsId: 'tomcatserverdetails1', path: '', url: 'http://34.27.4.180:8080')],
                       contextPath: '/app',
                       war: '**/*.war'
            }
        }
    }
    
    post {
        always {
            echo "Pipeline completed."
        }
        success {
            echo "Pipeline executed successfully."
            // Notify Slack channel about successful execution
            slackSend(channel: 'youtubejenkins', message: 'Success')
        }
        failure {
            echo "Pipeline execution failed."
            // Notify Slack channel about failed execution
            slackSend(channel: 'youtubejenkins', message: 'Job Failed')
        }
    }
}
