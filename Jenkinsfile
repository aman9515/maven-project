
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
                withSonarQubeEnv('SONAR_HOME') {
                    sh '''
                        set -e

                        mvn -B org.sonarsource.scanner.maven:sonar-maven-plugin:5.5.0.6356:sonar \
                            -Dsonar.projectKey=maven-project \
                            -Dsonar.projectName=maven-project
                    '''
                }
            }
        }

        stage('Artifact Upload') {
            steps {
                echo 'Uploading WAR artifact to JFrog Artifactory.'

                sh '''
                    set -e
                    test -f target/webapp.war
                '''

                rtUpload(
                    serverId: 'ARTIFACTORY',
                    spec: '''{
                        "files": [
                            {
                                "pattern": "target/webapp.war",
                                "target": "local-snapshot-repo/"
                            }
                        ]
                    }'''
                )

                echo 'JFrog artifact upload step completed.'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Preparing WAR artifact for Tomcat deployment.'

                sh '''
                    set -e
                    test -f target/webapp.war
                    ls -lh target/webapp.war

                    echo "WAR file is ready for deployment."
                '''

                echo 'Tomcat deployment command must be configured for your server.'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the Console Output for the failing stage.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
