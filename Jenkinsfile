pipeline {
    agent any
    parameters {
        string(name: 'MAVEN_GOAL', defaultValue: 'clean package ', description: 'maven goal')
    }
    triggers {
        
        pollSCM('* * * * *')
    }
    stages {
        stage('vcs') {
            steps {
                git branch: "google", url: 'https://github.com/spring-projects/spring-petclinic.git'
            }
            
        }
         stage ('Artifactory configuration') {
            steps {
                
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "jfrog",
                    releaseRepo: 'nevergiveup-libs-release-local',
                    snapshotRepo: 'nevergiveup-libs-snapshot-local'
                )

            }
        }
        stage('Build the Code') {
            steps {
                withSonarQubeEnv('SONAR_SELF_HOSTED') {
                    sh script: "mvn clean package sonar:sonar"
                }
            }
        }

        //stage("Quality Gate") {
        //    steps {
        //      timeout(time: 1, unit: 'HOURS') {
        //        waitForQualityGate abortPipeline: true
        //      }
        //    }
        //  }

          stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: 'mvn-3.6.3', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER"
                )
            }
        }
        
    }
}
