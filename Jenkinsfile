pipeline {
  agent any
  stages {
    stage('Build preparations') {
      steps {
        script {
          gitCommitHash = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
          shortCommitHash = gitCommitHash.take(7)
          // calculate a sample version tag
          VERSION = shortCommitHash
          // set the build display name
          currentBuild.displayName = "#${BUILD_ID}-${VERSION}"
          IMAGE = "$PROJECT:$VERSION"
        }

      }
    }
    stage('Docker build') {
      steps {
        script {
          docker.build("trusthcs/ecr-demo:1.0.0.101",".")
        }

      }
    }
    stage('Docker tag') {
      steps {
        script {
          docker.tag("trusthcs/ecr-demo:1.0.0.101", "659218023839.dkr.ecr.us-east-1.amazonaws.com/trusthcs/ecr-demo:1.0.0.101")
        }

      }
    }
    stage('Docker configuration') {
	  steps {  
        sh("aws ecr get-login --no-include-email --region us-east-1 | xargs xargs")
      }
    }
    
    stage('Docker push') {
      steps {
	    script {  
	      docker.withRegistry("http://659218023839.dkr.ecr.us-east-1.amazonaws.com") {  
	  	    docker.image("trusthcs/ecr-demo:1.0.0.101").push(env.BUILD_NUMBER)
	  	  }  
	  	}
      }
    }
  }
  environment {
    VERSION = 'latest'
    PROJECT = 'docker-pipline-demo'
    IMAGE = 'docker-pipline-demo:latest'
    ECRURL = 'http://659218023839.dkr.ecr.us-east-1.amazonaws.com'
    ECRCRED = 'ecr:us-east-1:jenkins-aws-ecr'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
  }
}