node('jdk7') {

	stage 'build'
		env.PATH="${tool 'mvn-3.3.3-x'}/bin:${env.PATH}"
		checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/issc29/workflow-demo']]])
		sh 'mvn clean package'
		archive 'target/*.war'

	stage 'integration-test'
		sh 'mvn verify'
		step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml', healthScaleFactor: 1.0])
}

if (env.BRANCH_NAME.length() > 2 && env.BRANCH_NAME.substring(0,3) == "PR-")
{
		return 0
}

stage 'quality-and-functional-test'

	parallel(qualityTest: {
    	node('jdk7') {
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
