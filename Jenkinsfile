pipeline{
    agent any
    tools{
        maven "MAVEN3"
        jdk "OracleJDK8"
    }
    stages{
        stage('Fetch the code'){
            steps{
                git branch: 'vp-rem' , url : 'https://github.com/devopshydclub/vprofile-repo.git'
            }
        }
        stage('Build with maven'){
            steps{
                sh 'mvn install -DskipTest'
            }
             post{
                 success{
                     sh 'echo "the build is successful"'
                 }
             }
        }
         stage('Test with maven'){
             steps{
                 sh 'mvn test'
             }
         }
         stage('checkstyle analysis'){
             steps{
                 sh 'mvn checkstyle:checkstyle'
             }
         }
         stage('pushing to sonarQube'){
            environment{
                scannerHome = tool 'Sonar4.7'
            }
            steps{
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
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
