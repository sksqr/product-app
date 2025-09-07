pipeline {
    agent any

    environment {
        EC2_USER = 'ubuntu'  // Update with your EC2 username
        EC2_HOST = 'ec2-65-0-29-120.ap-south-1.compute.amazonaws.com'  // Replace with your EC2 instance IP
        JAR_NAME = 'product-app-0.0.1-SNAPSHOT.jar'  // Change to your actual JAR file name
        DEPLOY_PATH = '/home/ubuntu/projects/build'  // Directory where JAR should be placed
    }

    // tools {
    //     // Install the Maven version configured as "M3" and add it to the path.
    //     maven "mvn3.9.9"
    // }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/sksqr/product-app.git'
            }
        }

        stage('Build and Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Run Unit Tests') {
                    steps {
                        sh 'mvn test'
                    }
                    post {
                        always {
                            junit '**/target/surefire-reports/*.xml'  // Collect test reports
                        }
                    }
               }

        stage('Upload JAR to EC2') {
            steps {
                sh "scp -i  /Users/shashikant.kushwaha/gfg/aws/jbdl85-ec2.pem target/${JAR_NAME} ${EC2_USER}@${EC2_HOST}:${DEPLOY_PATH}/"
            }
        }

        stage('Replace JAR') {
            steps {
                sh "ssh -i /Users/shashikant.kushwaha/gfg/aws/jbdl85-ec2.pem  ${EC2_USER}@${EC2_HOST}  'mv /home/ubuntu/projects/build/product-app-0.0.1-SNAPSHOT.jar /home/ubuntu/projects/product-app.jar'"
            }
        }

        stage('Restart product-app Service') {
            steps {
                sh "ssh -i /Users/shashikant.kushwaha/gfg/aws/jbdl85-ec2.pem  ${EC2_USER}@${EC2_HOST}  'sudo systemctl restart product-app'"
            }
        }

    }

    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}

