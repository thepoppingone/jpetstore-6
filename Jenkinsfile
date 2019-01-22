pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install -Dlicense.skip=true'
        echo 'Starting Build Step'
        echo 'Build Step Complete'
      }
    }
    stage('Testing stage') {
      parallel {
        stage('Sonarqube Test') {
          steps {
            sh 'mvn sonar:sonar -Dsonar.host.url=http://<IP address>:8081 -Dlicense.skip=true'
          }
        }
        stage('Print Tester Credentials') {
          steps {
            echo "The tester is ${TESTER}"
            sleep 10
          }
        }
        stage('Print Build Number') {
          steps {
            echo "This is build number ${BUILD_ID}"
            sleep 20
          }
        }
      }
    }
    stage('Jfrog push') {
      steps {
        echo 'Start Jfrog push'
        script {
          def server = Artifactory.server "artifactory"
          def buildInfo = Artifactory.newBuildInfo()
          def rtMaven = Artifactory.newMavenBuild()

          rtMaven.tool = 'maven'
          rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'

          buildInfo = rtMaven.run pom: 'pom.xml', goals: "clean install -Dlicense.skip=true"
          buildInfo.env.capture = true
          buildInfo.name = 'jpetstore-6'
          server.publishBuildInfo buildInfo
        }

        echo 'JFrog push finished'
      }
    }
  }
}