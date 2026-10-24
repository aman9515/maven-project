
pipeline {
    agent {
        label 'Slave_Linux'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm

                echo 'Source code checkout completed successfully.'

                sh 'ls -la'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    set -e

                    echo "===== Java Version ====="
                    java -version

                    echo "===== Maven Version ====="
                    mvn -version

                    echo "===== Building WAR File ====="
                    mvn -B clean package

                    echo "===== Verifying WAR File ====="
                    test -f target/webapp.war
                    ls -lh target/webapp.war
                '''

                archiveArtifacts(
                    artifacts: 'target/webapp.war',
                    fingerprint: true
                )
            }
        }

        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        mvn -B sonar:sonar \
                            -Dsonar.projectKey=maven-project \
                            -Dsonar.projectName=maven-project
                    '''
                }
            }
        }

        stage('Artifact Upload') {
            steps {
                echo 'Artifact Upload stage started.'

                echo 'TODO: Configure the JFrog Artifactory upload command and credentials.'

                sh '''
                    test -f target/webapp.war
                    echo "WAR artifact is ready for upload."
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deployment stage started.'

                echo 'TODO: Configure the Tomcat server, deployment credentials, and deployment command.'

                sh '''
                    test -f target/webapp.war
                    echo "WAR artifact is ready for deployment."
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Please check the Jenkins Console Output.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
