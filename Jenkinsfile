parameters {
        string(name: 'branch', defaultValue: 'master', description: 'branch to test')		
		string(name: 'testServerUrl', defaultValue: 'https://api-qa.groupdocs.cloud', description: 'server url')		
}

def runtests(dockerImageVersion)
{
    dir(dockerImageVersion){
        try {
            stage('checkout'){
				checkout([$class: 'GitSCM', branches: [[name: params.branch]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'LocalBranch', localBranch: "**"]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '361885ba-9425-4230-950e-0af201d90547', url: 'https://git.auckland.dynabic.com/assembly-cloud/assembly-ruby-sdk.git']]])
                withCredentials([usernamePassword(credentialsId: '6179b564-811f-48a9-8b85-875076fadbf5', passwordVariable: 'AppKey', usernameVariable: 'AppSid')]) {
					sh 'mkdir -p Settings'
                    sh 'echo "{\\"AppSid\\": \\"$AppSid\\",\\"AppKey\\": \\"$AppKey\\", \\"BaseUrl\\": \\"$testServerUrl\\"}" > Settings/servercreds.json'
                }
            }
            
            docker.image('ruby:' + dockerImageVersion).inside('-u root'){
                stage('build'){
						sh "mkdir testReports"
						sh "gem install bundler -v 2.0.2 && bundle install"
                }
            
                stage('tests'){   
					try{
						sh 'rake test'
					} finally{
						junit 'testReports/report/*.xml'
					}
                }
            
                stage('bdd-tests'){
					
                }
				
				stage('clean-compiled'){
					sh "rm -rf %s"
				}
            }        
        } finally {
			cleanWs()
        }
    }
}

node('words-linux') {        
    stage('oldruby'){
		try {
			runtests("2.4")
		} finally {
			cleanWs()
		}
	}
	
	stage('newruby'){
		try {
			runtests("2.5") 
		} finally {
			cleanWs()
		}
 	}
}