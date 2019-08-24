pipeline {
 environment {
    dockerhub_repo = 'himanshuchourasia/train-schedule'
    dockerhub_credential = 'dockerhub_credentials' 
 }

    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build and push  docker image') {
           when {
               branch 'master'
               
           }
           steps{
         	script {
    			def myImage = docker.build("${dockerhub_repo}:${env.BUILD_ID}")
    			docker.withRegistry('https://registry.hub.docker.com', dockerhub_credential) {
    			myImage.push()
    			myImage.push('latest')
    		
     }
    			
}     
               
           }
       }    
           
           stage('deploy container to production'){
             when {
                 branch 'master'
                 
             }

             steps{
                
 				input 'Deploy container to production ?'
                milestone label:'container ready for production', ordinal:1
				kubernetesDeploy( 
				configs: 'train_schedule_kube_config.yaml', 
				kubeconfigId: 'kube_config',
				enableConfigSubstitution: true
				)     			           


 				  

		
 			          
            }
         }                        
       }
     }
   