pipeline {
    agent any

    tools {
      maven 'Maven 3.6'
      }

    stages{
        stage('checkout'){
            steps{
                // Get some code from a GitHub repository
                git 'https://github.com/macuestap/simple-java-maven-app'
              }
        }
        stage('test'){
            steps{
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('build'){
           steps{
             post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
            }
        }
      }
    }
  }
}
