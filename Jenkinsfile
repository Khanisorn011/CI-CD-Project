def containerName="ci-cd"
def tag="latest"
def dockerHubUser="khanisorn011"
def gitURL="https://github.com/Khanisorn011/CI-CD-Project.git"

node {
    def sonarscanner = tool name: 'SonarQubeScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    stage('Checkout') {
        checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: gitURL]]]
    }

    stage('Build'){
        sh "mvn clean install"
    }

	stage('Docker Login') {
    	withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
        	sh 'echo $dockerPassword | docker login -u khanisorn011 --password-stdin'
    	}
	}

    stage("Image Prune"){
         sh "docker image prune -f"
    }

    stage('Image Build'){
        sh "docker build -t $containerName:$tag --pull --no-cache ."
        echo "Image build complete"
    }

    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh 'echo $dockerPassword | docker login -u khanisorn011 --password-stdin'
            sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
            sh "docker push $dockerUser/$containerName:$tag"
            echo "Image push complete"
        }
    }
	
   stage("SonarQube Scan"){
        withSonarQubeEnv(credentialsId: 'SonarQubeToken') {
			sh "${sonarscanner}/bin/sonar-scanner"
		}
    }
    /* 
    stage("Ansible Deploy"){
        ansiblePlaybook inventory: 'hosts', playbook: 'deploy.yaml'
    }
    */
}
