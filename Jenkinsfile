pipeline {
    agent any

    tools {
      maven 'Maven 3.6'
      }

      environment {
          // This can be nexus3 or nexus2
          NEXUS_VERSION = "nexus3"
          // This can be http or https
          NEXUS_PROTOCOL = "http"
          // Where your Nexus is running
          NEXUS_URL = "nexus:8081"
          // Repository where we will upload the artifact
          NEXUS_REPOSITORY = "jenkins"
          // Jenkins credential id to authenticate to Nexus OSS
          NEXUS_CREDENTIAL_ID = "NEXUS"
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
        stage("Maven Build") {
              steps {
                  script {
                      sh "mvn package -DskipTests=true"
                  }
              }
          }
          stage("Publish to Nexus Repository Manager") {
              steps {
                  script {
                      pom = readMavenPom file: "pom.xml";
                      filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                      echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                      artifactPath = filesByGlob[0].path;
                      artifactExists = fileExists artifactPath;
                      if(artifactExists) {
                          echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                          nexusArtifactUploader(
                              nexusVersion: NEXUS_VERSION,
                              protocol: NEXUS_PROTOCOL,
                              nexusUrl: NEXUS_URL,
                              groupId: pom.groupId,
                              version: pom.version,
                              repository: NEXUS_REPOSITORY,
                              credentialsId: NEXUS_CREDENTIAL_ID,
                              artifacts: [
                                  [artifactId: pom.artifactId,
                                  classifier: '',
                                  file: artifactPath,
                                  type: pom.packaging],
                                  [artifactId: pom.artifactId,
                                  classifier: '',
                                  file: "pom.xml",
                                  type: "pom"]
                              ]
                          );
                      } else {
                          error "*** File: ${artifactPath}, could not be found";
                      }
                    }
                  }
                }
              }
            }
