pipeline {

  agent {
    label 'Slave_Induccion'
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '3'))
 	 disableConcurrentBuilds()
  }

  tools {
    node 'node_14_17_5'
  }

  stages {
    stage('Checkout') {
      steps{
        echo "------------>Checkout<------------"
        checkout([
			$class: 'GitSCM',
			branches: [[name: "*/${APP_BRANCH_NAME}"]],
			doGenerateSubmoduleConfigurations: false,
			extensions: [],
			gitTool: 'Default',
			submoduleCfg: [],
			userRemoteConfigs: [[
				credentialsId: 'redsuelva_gitlab',
				url:'https://gitlab.com/crm3/frontend-web.git'
			]]
		])
      }
    }

    stage('Install') {
      steps {
          echo "------------>Install<------------"
          sh 'npm install' 
          
      }
    }
    
    stage('Build') {
      steps { 
          echo "------------>Build<------------"
          sh 'node --max_old_space_size=8192 node_modules/@angular/cli/bin/ng build --prod ' 
          
      }
    }

    stage('publish') {
      steps{
        echo "------------>Publish<------------"
        zip zipFile: 'dist.zip', archive: false, dir: 'dist/kanri'
        archiveArtifacts artifacts: 'dist.zip', fingerprint: true
        
        sshagent(credentials: ['ssh-credentials-id']){
            scp ./dist.zip user@51.79.51.89:/home/centos
        }

      }
    }

  }

}
