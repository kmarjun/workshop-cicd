pipeline {
    agent {
        node {
            label 'jenkins-slave'
        }
    }
    tools {
        maven 'maven-tool'
        jdk 'jdk11'
    }
    stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/ravdy/tweet-trend-new.git'
            }
        }

        stage('Maven Clean') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn install -Dmaven.test.skip=true'
            }
        }

        stage('unit test') {
            steps {
                sh 'mvn surefire-report:report'
            }
        }

        stage('Sonar Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonar-scanner'
                    withSonarQubeEnv('sonar-server') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.organization=kmarjun -Dsonar.projectKey=kmarjun_workshop-cicd -Dsonar.projectName=workshop-cicd -Dsonar.language=java -Dsonar.sources=. -Dsonar.java.binaries=target/classes -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml"
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 2, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to low quality: ${qg.status}"
                        }
                    }
                }
            }
        }
    }
}
