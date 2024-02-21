pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo "Building..."
            }
            post {
                always {
                    jiraSendBuildInfo()
                }
           }
        }
        stage('Deploy - Test') {
            steps {
                echo "Deploying to test"
            }
        }
        stage('Deploy - Stage') {
            steps {
                echo "Deploying to staging"
            }
        }
        stage('Request approval') { // Raise change request
            steps {
                echo 'Raise change request...'
                jiraSendDeploymentInfo(site:'fintechinnovacion.atlassian.net',
                        environmentId:'prod-1',
                        environmentName:'prod-1',
                        environmentType:'production',
                        state:"pending",
                        enableGating:true, 
                        serviceIds: [
                          'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzVlMDVkZGY4LTgyYTUtNGU0Mi05MjA3LTNjZWE3ZmJkZjg3MC9jNDJhMzY5Yy1iYzM3LTExZWUtODU1Mi0wYWJlM2Y0YTY2MDE='
                        ]
                    )
            }
        }
        stage('Approval gate') { // Check change request status
            steps {
                retry(20) { // Poll every 30s for 10min
                    waitUntil { 
                        sleep 30
                        checkGatingStatus(
                          site:'fintechinnovacion.atlassian.net', 
                          environmentId:'prod-1'
                        )
                    }
                }   
            }
        }
        stage('Deploy - Production') {
            steps {
                echo "Deploying to production!!"
            }
            post {
              always {
                  jiraSendDeploymentInfo (
                      site: 'fintechinnovacion.atlassian.net',
                      environmentId: 'prod-1',
                      environmentName: 'prod-1',
                      environmentType: 'production',
                      state: 'successful',
                      serviceIds: [
                        'b:YXJpOmNsb3VkOmdyYXBoOjpzZXJ2aWNlLzVlMDVkZGY4LTgyYTUtNGU0Mi05MjA3LTNjZWE3ZmJkZjg3MC9jNDJhMzY5Yy1iYzM3LTExZWUtODU1Mi0wYWJlM2Y0YTY2MDE='
                      ]
                    )
              }
            }
        }
    }
}
