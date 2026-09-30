pipeline {
    agent none

    stages {

        stage('CI on AgentA') {

            agent {
                label 'AgentA'
            }

            stages {

                stage('Checkout') {
                    steps {
                        checkout scm

                        sh '''
                            echo "===== CHECKOUT ====="
                            hostname
                            git log -1 --oneline
                        '''
                    }
                }

                stage('Compile') {
                    steps {
                        sh '''
                            echo "===== COMPILE ====="
                            mvn -B clean compile
                        '''
                    }
                }

                stage('Test') {
                    steps {
                        sh '''
                            echo "===== TEST ====="
                            mvn -B test
                        '''
                    }

                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
                    }
                }

                stage('Package') {
                    steps {
                        sh '''
                            echo "===== PACKAGE ====="
                            mvn -B package -DskipTests
                            ls -lh target/addressbook.war
                        '''
                    }
                }

                stage('Archive and Stash') {
                    steps {

                        archiveArtifacts(
                            artifacts: 'target/addressbook.war',
                            fingerprint: true
                        )

                        stash(
                            name: 'war-file',
                            includes: 'target/addressbook.war'
                        )
                    }
                }
            }
        }

        stage('CD on AgentB') {

            agent {
                label 'AgentB'
            }

            steps {

                unstash 'war-file'

                sh '''
                    set -e

                    echo "===== ARTIFACT ====="
                    ls -lh target/addressbook.war

                    echo "===== DEPLOY ====="

                    rm -rf /opt/tomcat/webapps/addressbook
                    rm -f /opt/tomcat/webapps/addressbook.war

                    cp target/addressbook.war \
                       /opt/tomcat/webapps/addressbook.war

                    echo "===== VERIFY ====="

                    for i in $(seq 1 30); do

                        code=$(curl -s -o /dev/null \
                          -w '%{http_code}' \
                          http://localhost:8080/addressbook/ || true)

                        echo "Attempt $i: HTTP $code"

                        if [ "$code" = "200" ]; then
                            echo "DEPLOYMENT VERIFIED"
                            exit 0
                        fi

                        sleep 2

                    done

                    echo "DEPLOYMENT FAILED"
                    exit 1
                '''
            }
        }
    }
}
