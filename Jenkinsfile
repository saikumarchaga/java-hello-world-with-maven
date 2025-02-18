pipeline {
    agent any

    tools {
        maven 'maven-proj' // Specify the name you configured in Global Tool Configuration
        jdk 'Java-home'    // Specify the JDK name
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/saikumarchaga/java-hello-world-with-maven.git' // Replace with your Git repo URL
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Execute SonarQube analysis
                withSonarQubeEnv('sonarqube') { // 'SonarQube' is the name configured in Jenkins
                sh ''' mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=java-maven-project \
                    -Dsonar.host.url=http://13.233.225.46:9000 \
                    -Dsonar.login=sqa_cffcf75a6ea4c76ff30829d6d19e92b303cf6483 '''

                }
            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    // Wait for the SonarQube analysis to complete and check the quality gate
                    def qualityGate = waitForQualityGate(timeout: '300') // 300 seconds
                    //def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
