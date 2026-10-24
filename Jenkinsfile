@Library('cve-scan') _

pipeline {
  agent { label 'build' }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Build') {
      steps {
        sh '''
          docker run --rm -u $(id -u):$(id -g) -e HOME=/tmp -v "$PWD":/app -w /app \
            maven:3.9-eclipse-temurin-21 mvn -B -Dmaven.repo.local=/tmp/.m2 package -DskipTests
        '''
      }
    }
    stage('Test') {
      steps {
        sh '''
          docker run --rm -u $(id -u):$(id -g) -e HOME=/tmp -v "$PWD":/app -w /app \
            maven:3.9-eclipse-temurin-21 mvn -B -Dmaven.repo.local=/tmp/.m2 test
        '''
      }
    }
    stage('Dependency scan') { steps { script { cveScan.scan() } } }
    stage('CVE evaluate')    { steps { script { cveScan.evaluate(projectName: 'demo-spring', threshold: 7.0) } } }
  }
}