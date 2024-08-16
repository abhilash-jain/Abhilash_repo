pipeline {
   agent {
      kubernetes {
	   inheritFrom 'default'
	   
	   }
	 }

environment {
  //Nexus to some of these pods template
 NEXUS_URL = 'https://nexus-cicd.apps.sit.rcp.apuat.local'
PACKAGE_PATH = 'repository/Pro_Active/com/mufg/pro_active_release'
PACKAGE_NAME = 'IncrementalInstaller_ProdRelV10.4.7.21.tar.zip
//MD5_FILE = 'update.md5'
BITBUCKET_CREDENTIALS_ID = 'jenkinsadmin'
NEXUS_CREDENTIALS_ID = 'nexus'

// WILL get from agent environment variables
BITBUCKET_EMAIL = 'jenkinsadmin@apuat.local'
BITBUCKET_USER = 'jenkinsadmin'
BITBUCKET_DOMAIN = sh ( returnStdout: true, script: "echo $GIT_URL | awk -F[/:] '{print \$4}'").trim()
BITBUCKET_PROJECT = 'ASO-DevOps'
BITBUCKET_REPOS = 'ASO-DevOps-ProActive'

}

stages {

stage('Prepare Deployment') {
// This is to upload vendor packages file to nexus server in predefined pattern
// If required, need to rename files
// can be multiple stages if required.
// Here we will define what components are required

when {
     branch 'develop'
	 not{changelog '.*package\\.yaml.+$'}
	 }
	  
stages {
    stage ('Update parameters') {
	input {
	message " Please provide package information"
	ok "Submit"
	parameters {
	string(name: 'Folder_NAME',defaultValue: '',description: 'Folder Name uploaded in Nexus.')
	string(name: 'RELEASE_VERSION',defaultValue: '',description: 'Release or Package Version uploaded in Nexus.Exampl')
	
	}
	
}

steps {
     echo "Writing package configuration to package.yaml in ${env.GIT_BRANCH}"
	 sh "printenv"
	 script {
	 // Fail the pipeline if Folder_NAME in empty.
	 If ("${Folder_NAME}" == '') {
	  System.exit(0)
	  
	  }
	  	 // Fail the pipeline if RELEASE_VERSION in empty.
	 If ("${RELEASE_VERSION}" == '') {
	  System.exit(0)
	  
	  }
	  def config = [
	  folder_name: "${Folder_NAME}",
	  release_version: "${RELEASE_VERSION}",
	  production: [
	  approved: false
	  ],
	  environments: [
	  UAT1: [],
	  UAT2: [],
	  UAT3: [],
	  UAT4: [],
	  UAT5: []
	  ]
	  
	  ]
	  
	  writeYaml file: 'package.yaml', data: config, overwrite: true
	  
	  try {
	    withCredentials([gitUsernamePassword(credentialsId: "${env.BITBUCKET_CREDENTIALS_ID}",
		                  gitToolName: 'git-tool' )]) {
						  
				sh 'export GIT_SSL_NO_VERIFY=true'
				sh 'git stash'
				sh 'git pull'
				sh 'git stash pop'
				sh 'git add package.yaml'
				sh "git commit -m 'updated package.yaml for release ${RELEASE_VERSION}'"
				sh 'git push origin HEAD:develop'
						}
			} catch (Exception e) {
              echo 'Exception occured: ' + e.toString()
            }
		}
	}
}
}
}
}
}
