pipeline {
    agent {
        label 'Linux_RHEL'
    }
    tools {
        nodejs 'Node12'
    }
    parameters {
        choice (
            name: 'RELEASE_TYPE',
            choices: ['DEVELOP', 'TEST', 'RELEASE'], 
            description: 'Release type selection (DEVELOP|TEST|RELEASE)')
        
        string (
            name: 'RELEASE_VER', 
            defaultValue: '0.1.1', 
            description: 'Release version (number value, format x.x.x)', 
            trim: false)
    }

    stages {
        stage('Get source') {
            steps {
                git "https://github.com/tovmariupol/mdt-fork"
            }
        }
        stage("Build') {
            parallel {
                stage("Build_CSS") {
                    steps {
                         sh """cd ${WORKSPACE}/www/css
                         cleancss -d style.css > ../min/custom-min.css"""
                    }
                }  
                stage("Build_JS") {
                    steps {
                         sh """cd ${WORKSPACE}/www/js
                         uglifyjs --timings init.js -o ../min/custom-min.js"""
                    }
                }
        }
        stage("Package_artifact") {
            steps {
                sh """cd ${WORKSPACE}/www
                  tar --exclude='./css' --exclude='./js' -c -z -f ../site-archive-${params.RELEASE}-${params.RELEASE_VER}-${BUILD_NUMBER}.tgz ."""
            }
        }

        stage('Archive') {
           when {
		expression {
                    param.RELEASE_TYPE == 'RELEASE'
                }
	   }
            steps {
                archiveArtifacts '*.tgz'
            }
        }
    }
}

