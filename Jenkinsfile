node('jdk8') {

	stage 'build'
		env.PATH="${tool 'M3'}/bin:${env.PATH}"
		checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/issc29/workflow-demo']]])
		sh 'mvn clean package'
		archive 'target/*.war'
		def test = env.BRANCH_NAME 
		if(test.substring(0,2) != "PR")
		{
			error "abc"
		}

	stage 'integration-test'
		sh 'mvn verify'
		step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml', healthScaleFactor: 1.0])
}

if (env.BRANCH_NAME.length() < 3 || env.BRANCH_NAME.substring(0,3) != "PR-")
{

stage 'quality-and-functional-test'

	parallel(qualityTest: {
    	node('jdk8') {
    		echo 'sonar scan'
        	// sh 'mvn sonar:sonar'
    	}
    }, functionalTest: {
    	echo 'selenium test'
        // build 'sauce-labs-test'
    })

    try {
        checkpoint('Testing Complete')
    } catch (NoSuchMethodError _) {
        echo 'Checkpoint feature available in Jenkins Enterprise by CloudBees.'
    }


stage 'approval'
	input 'Do you approve deployment to production?'

stage 'production'
	echo 'mvn cargo:deploy'

}
