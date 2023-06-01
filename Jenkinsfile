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
    }
}
