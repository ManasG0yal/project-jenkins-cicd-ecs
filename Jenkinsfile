pipeline{
    agent any
    tools{
        maven "MAVEN"
        jdk "OPENJDK8"
    }
    stages{
        stage('Fetch the code'){
            steps{
                git branch: 'vp-rem' , url : 'https://github.com/devopshydclub/vprofile-repo.git'
            }
        }
        stage('Build with maven'){
            steps{
                sh 'mvn install'
            }
//             post{
//                 success{
//                     sh 'echo "the build is successful"'
//                 }
//             }
        }
//         stage('Test with maven'){
//             steps{
//                 sh 'mvn test'
//             }
//         }
//         stage('checkstyle analysis'){
//             steps{
//                 sh 'mvn checkstyle:checkstyle'
//             }
//         }
    }
}
