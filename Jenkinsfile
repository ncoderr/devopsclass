pipeline {
	agent any

    environment {
        NEXUS_IP = '192.168.3.91'
        NEXUS_PORT = '8081'
        NEXUS_REPOSITORY = "vpro-release"
        NEXUS_GROUP = "QA"
        FILE_PATH = 'target/vprofile-v2.war'
        ARTIFACT_ID = 'vprofile'
        VERSION = "${env.BUILD_TIMESTAMP}-${env.BUILD_ID}"
        NEXUS_INSTANCE_ID = 'NexusRepoMgr'
        INVENTORY_PATH_STAGE = 'ansible/inventory-stage'
        INVENTORY_PATH_PROD = 'ansible/inventory-prod'
        ANSIBLE_CRED_ID = 'vagrant'
        ANSIBLE_VAULT_CRED_ID = 'vault4nexus'
    }

    stages {

        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: FILE_PATH
                }
            }
        }

    	stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

	    stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage("Publish to Nexus Repository Manager") {
          steps {
            nexusPublisher nexusInstanceId: NEXUS_INSTANCE_ID,
                           nexusRepositoryId: NEXUS_REPOSITORY,
                           packages: [[$class: 'MavenPackage',
                                       mavenAssetList: [[classifier: '', extension: '', filePath: FILE_PATH]],
                                       mavenCoordinate: [artifactId: ARTIFACT_ID, groupId: NEXUS_GROUP, packaging: 'war', version: VERSION]
                                     ]]
          }
        }


        stage('Deploy-to-Stage'){
            steps {
                  ansiblePlaybook (
                    playbook: 'ansible/site.yml',
                    inventory: INVENTORY_PATH_STAGE,
                    credentialsId: ANSIBLE_CRED_ID,
                    vaultCredentialsId: ANSIBLE_VAULT_CRED_ID,
                    disableHostKeyChecking: true,
                    extraVars: [
                      nexusip: NEXUS_IP,
                      nexusport: NEXUS_PORT,
                      reponame: NEXUS_REPOSITORY,
                      groupid: NEXUS_GROUP,
                      vprofile_version: VERSION,
                      artifactId: ARTIFACT_ID
                    ] )
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {

		  environment {
             scannerHome = tool 'sonarqube4class'
          }

          steps {
            withSonarQubeEnv('sonarqube4class') {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate(webhookSecretId: 'wh4class', abortPipeline: true)
            }
          }
        }

        stage('Deploy-to-Prod'){
            steps {
                  ansiblePlaybook(
                    playbook: 'ansible/site.yml',
                    inventory: INVENTORY_PATH_PROD,
                    credentialsId: ANSIBLE_CRED_ID,
                    vaultCredentialsId: ANSIBLE_VAULT_CRED_ID,
                    disableHostKeyChecking: true,
                    extraVars: [
                      nexusip: NEXUS_IP,
                      nexusport: NEXUS_PORT,
                      reponame: NEXUS_REPOSITORY,
                      groupid: NEXUS_GROUP,
                      vprofile_version: VERSION,
                      artifactId: ARTIFACT_ID
                    ])
            }
        }

    }

}