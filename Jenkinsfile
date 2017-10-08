#!groovy
properties([[$class: 'jenkins.model.BuildDiscarderProperty', strategy: [$class              : 'LogRotator',
                                                                        numToKeepStr        : '4',
                                                                        artifactNumToKeepStr: '1']]])

node {
  checkout scm
  def GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()

  stage('build') {
    withMaven(maven: 'M3') {
        sh("mvn clean compile")
    }
  }

  stage('test') {
    withMaven(maven: 'M3') {
      sh("mvn integration-test")
    }
    stash 'build'
  }

  stage('create beanstalk artifact') {
    sh("mkdir -p beanstalk && rm -Rf beanstalk/* && rm -Rf fastjson-${GIT_COMMIT}.zip && mv target/*.jar beanstalk/")
    zip archive: true, dir: 'beanstalk', glob: '*', zipFile: "fastjson-${GIT_COMMIT}.zip"
  }
}