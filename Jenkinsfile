pipeline{
	agent{
		label 'Master'	
	}
	
	tools {
		maven 'maven_3.9.0'
	}
	
	stages{
		stage('SCM Checkout'){
			steps{
				checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Karthi-289/java-maven-war-app.git']])
			}
		}
		
		stage('Mavan-Build'){
			steps{
				sh 'mvn clean install'
			}
		}
		
		stage('Sonar Scan'){
			steps{
				withSonarQubeEnv("SonarQube") {
					sh "${tool("Sonar_4.8")}/bin/sonar-scanner \
					-Dsonar.host.url=http://ec2-3-110-45-78.ap-south-1.compute.amazonaws.com:9000/ \
					-Dsonar.login=sqp_294a750f00dfbd3fd1c4d100664e1fe1f958721d \
					-Dsonar.projectKey=java-maven-app \
					-Dsonar.java.binaries=target"
					
				}
			}
		}
		
		stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }
		
		stage('Diployment'){
			agent{
				label 'Ansible'
			}
			steps{
				sh 'ansible-playbook -i inventory deploy_playbook.yml -e "build_number=${BUILD_NUMBER}"'
			}
		}
	}
	
}
