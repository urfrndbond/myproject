pipeline{
  agent any
  environment{
	imagename = "ajaynegi/php-project"
	dockerImage = ''
  }
	  stages{
		stage("Cloning Git"){
		  steps{
			echo 'Cloning Repo...'
			git([url: 'https://github.com/pranshulmahajan/PHP.git', branch: 'master', credentialsId: 'f441ae83-f772-4f36-aa05-d198d0814675'])
		  }
		}
		stage('Building image') {
			steps{
				script {
					dockerImage = docker.build imagename + ":$BUILD_NUMBER"
				}
			}
		}
		stage('Deploy Image') {
			steps{
				script {
					docker.withRegistry( '', 'eb562d7c-b70d-4595-abd8-4e6762a728c9	' ) {
						dockerImage.push("$BUILD_NUMBER")
						dockerImage.push('latest')
					}
				}
			}
		}
		stage('Archive artifacts, pull image and containerize') {
		  steps{
			script{
				archiveArtifacts artifacts: 'template/template.php', onlyIfSuccessful: true
				sh "docker pull $imagename:$BUILD_NUMBER"
				sh "docker images"
				sh "docker container run -d $imagename:$BUILD_NUMBER"
				sh "docker container ls"
			}
		  }
		}
		stage('Remove Unused docker image') {
			steps{
				script{
					sh "docker rmi -f $imagename:$BUILD_NUMBER"
					sh "docker rmi -f $imagename:latest"
					sh 'docker stop $(docker ps -a -q)'
					sh "docker container ls"
					sh "docker ps"
				}
			}
		}
	}
}
