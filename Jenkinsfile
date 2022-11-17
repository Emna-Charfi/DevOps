pipeline {

	agent any

	stages {
		
		stage('Junit + Mockito Test') {
			steps {
				sh 'mvn test'
			      } 
		}
		stage('Build Artifact - Maven') {
			steps {
				sh "mvn clean package -DskipTests=true"
				archive 'target/*.jar'
			      }
		}
		       
		stage('SonarQube + JacOcO Analysis') {
			steps {
				sh "mvn  sonar:sonar -Dsonar.projectKey=devops  -Dsonar.host.url=http://192.168.33.10:9000  -Dsonar.login=16222d068366e3683735f1a9a2c356437813e05a"
			}
		        post {
				always {
					jacoco execPattern: 'target/jacoco.exec'
				       }    
			    } 
		 }  
		 stage('Sonatype/Nexus deploy') {
			steps {
				//sh 'mvn clean deploy -DskipTests'
				sh'mvn clean deploy -Dmaven.test.skip=true -Dresume=false'
			      }
		 } 
		/* stage('Docker Build and Push') {
                       steps {
                               withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
         			  sh 'printenv'
        			  sh 'docker build -t rania24/springapp .'
	 			  sh 'docker tag rania24/springapp rania24/springapp:latest'
         			  sh 'docker push rania24/springapp:latest'
         			}
     			  }
    		}*/
		 stage('Docker compose') {
      		      steps {
         parallel(
           "Docker compose": {
               sh 'docker-compose up '
           },
           "Delete running containers": {
		       sh 'sleep 2m '
               sh 'docker rm -f ci-spring ci-db ci-angular '
           }
         )
       }
     }
	}  
			post {
				success {

					echo "passed"
				}    
			       failure {
				       echo "failed"
				
		                }
		}
}
