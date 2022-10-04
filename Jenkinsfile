pipeline {
    agent any
    environment {
	   // which region do you want to deploy in aws
    awsRegion = "us-east-1"
	  awsAccoundId = "856478999786"
    awsProdAccoundId = "856478999786"
	  notification_mail = "babu@ensarsolutions.com,navaneetha@ensarsolutions.com"   
	   // add the assume role which account you want to deploy 
	   awsAssumeRole = "${env.BRANCH_NAME == "develop" ? "${awsAssumeRole_dev}" : "${awsAssumeRole_dev}"}"
	   awsAssumeRole_dev = "arn:aws:iam::856478999786:role/tt-jenkins-deployemnt-role"


     S3_BUCKET_NAME = "tt-ecs-env-files"
	   SERVICE = "Automation"


	   // environment deployment
     ENVIRONMENT = "${env.BRANCH_NAME == "develop" ? "signaldev" : "signaldev"}"
	   GIT_COMMIT_AUTHOR = sh (script: "git log -n 1 --pretty=format:'%ae'", returnStdout: true)
     scmRevisionNumber = sh (script: "git log --format='%H' -n 1", returnStdout: true )
	   

	   
  }
  triggers {
    pollSCM ('* * * * *')
	// cron ('0 0 * * 1-5')
  }
  options {
      disableConcurrentBuilds()
      buildDiscarder(logRotator(numToKeepStr: '5', artifactDaysToKeepStr: '3', artifactNumToKeepStr: '1'))
      timeout(time:7, unit:'DAYS')
   }
   stages {
   stage('SeleniumTestCases'){
      steps {

          script {
                println("=================== Test Dev environment =============")
          withMaven(maven : 'maven') {
          sh 'mvn clean test'
          }
	   echo "================ Sending email notifications for Dev...... ================="
	  currentBuild.result = "SUCCESS"
          }
             
       }
     }	 

     stage('Cucumber Report') {
	    steps {
		    cucumber fileIncludePattern: '**/*.json', jsonReportDirectory: 'target/', sortingMethod: 'ALPHABETICAL', reportTitle: 'signal-dev'
		}
      }	 
    } 
   post {
      success {
      echo 'Successful build occured'

      script {
          currentBuild.result = "SUCCESS"
          cleanWs()
      }
    }

    failure {
      echo 'Failure occured sending notification'

      script {
        currentBuild.result = "FAILURE"
        cleanWs()
      }
      
    }
    // always {
    //         cucumber fileIncludePattern: '**/*.json', jsonReportDirectory: 'target/', reportTitle: 'signal-dev'
    //     }
   }
  }
