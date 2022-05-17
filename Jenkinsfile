pipeline {
    
	agent any
/*	
	tools {
        maven "maven3"
    }
*/	
    environment {
        NEXUS_IP = '192.168.3.91'
        NEXUS_PORT = '8081'
        NEXUS_INSTANCE_ID = 'NexusRepoMgr'
        NEXUS_REPOSITORY = "vpro-release"
        NEXUS_GROUP = "QA"
        FILE_PATH = 'target/vprofile-v2.war'
        ARTIFACT_ID = 'vprofile'
        VERSION = "${env.BUILD_TIMESTAMP}-${env.BUILD_ID}"
        INVENTORY_PATH_STAGE = 'ansible/inventory_stage'
        INVENTORY_PATH_PROD = 'ansible/inventory_prod'
        ANSIBLE_CRED_ID = 'vagrant'
        ANSIBLE_VAULT_CRED_ID = 'ansible-vault-pass'
    }

    stages{

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
            post {
                success {
                    echo 'Generated Analysis Result'
                }
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
                  ansiblePlaybook(
                    playbook: 'ansible/site.yml',
                    inventory: INVENTORY_PATH_STAGE,
                    credentialsId: ANSIBLE_CRED_ID,
                    vaultCredentialsId: ANSIBLE_VAULT_CRED_ID,
                    hostKeyChecking: False,
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


        stage('Deploy-to-Prod'){
            steps {
                  ansiblePlaybook(
                    playbook: 'ansible/site.yml',
                    inventory: INVENTORY_PATH_PROD,
                    credentialsId: ANSIBLE_CRED_ID,
                    vaultCredentialsId: ANSIBLE_VAULT_CRED_ID,
                    hostKeyChecking: False,
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
