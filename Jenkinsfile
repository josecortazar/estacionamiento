
pipeline {
  //Donde se va a ejecutar el Pipeline
  agent {
    label 'Slave_Induccion'
  }

  //Opciones espec�ficas de Pipeline dentro del Pipeline
  options {
    	buildDiscarder(logRotator(numToKeepStr: '3'))
 	disableConcurrentBuilds()
  }

  //Una secci�n que define las herramientas �preinstaladas� en Jenkins
  tools {
    jdk 'JDK8_Centos' //Verisi�n preinstalada en la Configuraci�n del Master
  }
/*	Versiones disponibles
      JDK8_Mac
      JDK6_Centos
      JDK7_Centos
      JDK8_Centos
      JDK10_Centos
      JDK11_Centos
      JDK13_Centos
      JDK14_Centos
*/

  //Aqu� comienzan los �items� del Pipeline
  stages{
    stage('Checkout'){
        steps{
            echo "------------>Checkout<------------"
            checkout([
                $class: 'GitSCM', 
                branches: [[name: '*/master']], 
                doGenerateSubmoduleConfigurations: false, 
                extensions: [], 
                gitTool: 'Default', 
                submoduleCfg: [], 
                userRemoteConfigs: [[
                    credentialsId: 'GitHub_josecortazar', 
                    url:'https://github.com/josecortazar/estacionamiento'
            	]]
        	])
   		}
	}

    stage('Compile & Unit Tests') {
        steps{
        	sh './gradlew clean'
            echo "------------>compile & Unit Tests<------------"
            sh 'chmod +x gradlew'
            sh './gradlew --b ./build.gradle test'
        }
    }


    stage('Static Code Analysis') {
        steps{
            echo '------------>An�lisis de c�digo est�tico<------------'
            withSonarQubeEnv('Sonar') {
                sh "${tool name: 'SonarScanner', type:'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner"
            }
        }
    }


    stage('Build') {
        steps{
            echo "------------>Build<------------"
            //Construir sin tarea test que se ejecut� previamente
            sh './gradlew --b ./build.gradle build -x test'
        }
    }


  post {
    always {
      echo 'This will always run'
    }
    success {
      echo 'This will run only if successful'
    }
    failure {
      echo 'This will run only if failed'
    }
    unstable {
      echo 'This will run only if the run was marked as unstable'
    }
    changed {
      echo 'This will run only if the state of the Pipeline has changed'
      echo 'For example, if the Pipeline was previously failing but is now successful'
    }
  }
}