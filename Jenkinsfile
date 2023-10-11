node{
    
    def tag, dockerHubUser, containerName, httpPort = ""
    
    stage('Prepare Environment'){
        echo 'Initialize Environment'
        tag="3.0"
	withCredentials([usernamePassword(credentialsId: 'Dockerhub', usernameVariable: 'johnkalayu', passwordVariable: 'PAPILLONJ')]) {
		dockerHubUser="johnkalayu"
        }
	containerName="bankingapp"
	httpPort="8989"
    }
    
    stage('Code Checkout'){
        try{
            checkout scm
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
        }
    }
    
    stage('Maven Build'){
        sh "mvn clean package"        
    }
    
    stage('Docker Image Build'){
        echo 'Creating Docker image'
        sh "docker build -t johnkalayu/bankingapp ."
    }  
	
    stage('Publishing Image to DockerHub'){
        echo 'Pushing the docker image to DockerHub'
        withCredentials([usernamePassword(credentialsId: 'Dockerhub', usernameVariable: 'johnkalayu', passwordVariable: 'PAPILLONJ')]) {
		sh "docker login -u johnkalayu -p PAPILLONJ"
		sh "docker push johnkalayu/bankingapp"
		echo "Image push complete"
        } 
    }    
	
	stage('Ansible Playbook Execution'){
		sh "ansible-playbook -i inventory.yaml kubernetesDeploy.yaml -e httpPort=8989 -e containerName=bankingapp -e dockerImageTag=johnkalayu/bankingapp:latest"
	}
}


