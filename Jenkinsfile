def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    tools {
	    maven "MAVEN3"
      jdk "OracleJDK8"
	}
    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = "877147146763.dkr.ecr.us-east-1.amazonaws.com/proj-ecs-cicd"//image name or 
        vprofileRegistry = "https://877147146763.dkr.ecr.us-east-1.amazonaws.com"//registry url
        cluster = 'jenkins-proj-cluster'
        service = 'proj-jenkins-ecs-service'
    }
  stages {
    stage('Fetch code'){
      steps {
        git branch: 'docker', url: 'https://github.com/devopshydclub/vprofile-project.git'
      }
    }


    stage('Test'){
      steps {
        sh 'mvn test'
      }
    }

    stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

        stage('build && SonarQube analysis') {
            environment {
             scannerHome = tool 'Sonar4.7'
          }
            steps {
                withSonarQubeEnv('sonar'){
                    sh ''' 
                    ${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Proj-ecs \
                    -Dsonar.projectName=Proj-repo \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml \
                     '''
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

    stage('Build App Image') {
       steps { 
         script {
                dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./Docker-files/app/multistage/")
             }
     }
    
    }
    stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( vprofileRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
     }
     stage('Deploy to ecs'){
          steps {
        withAWS(credentials: 'awscreds', region: 'us-east-1') {
          sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
        }
     }
  }
  post {
        always {
            echo 'Slack Notifications.'
            slackSend channel: '#project-channel',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
  }
}
