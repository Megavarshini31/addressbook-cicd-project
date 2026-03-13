pipeline {
    tools {
        maven 'mymaven'
    }
    agent any

    stages {

        stage('CloneRepo') {
            steps {
                echo 'Cloning the repository...'
                git 'https://github.com/akshu20791/addressbook-cicd-project'
            }
        }

        stage('Compile') {
            steps {
                echo 'Compiling the code...'
                sh 'mvn compile'
            }
        }

        stage('CodeReview') {
            steps {
                echo 'Running PMD code review...'
                sh 'mvn pmd:pmd'
            }
            post {
                success {
                    recordIssues(tools: [pmdParser(pattern: '**/pmd.xml')])
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo 'Checking Quality Gate...'
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
            post {
                success {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging the application...'
                sh 'mvn package'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Tomcat...'
                sh 'sudo mv /var/lib/jenkins/workspace/addressbook-cicd/target/addressbook.war /home/ubuntu/apache-tomcat-9.0.115/webapps'
            }
        }

    }
}
