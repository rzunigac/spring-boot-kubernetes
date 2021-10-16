pipeline {
  agent any
  
  tools {
    maven 'Maven'
  }
  
  stages {
    stage ('Initial') {
      steps {
        sh '''
          echo "PATH = ${PATH}"
          echo "M2_HOME = ${M2_HOME}"
        '''
      }
    }
    
    stage ('Compile') {
      steps {
        sh 'mvn clean compile -e'
      }
    }
    
    stage('Sonarqube'){
      steps{

        script{
          def scannerHome = tool 'SonarScanner'

          PROJECT_NAME = sh(
              script: 'echo $JOB_NAME | sed --expression=\'s#/#_#g\'',
              returnStdout: true
          ).trim()

          withSonarQubeEnv('SonarServer'){
             sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${PROJECT_NAME} -Dsonar.projectName=${PROJECT_NAME} -Dsonar.sources=. -Dsonar.projectBaseDir=${env.WORKSPACE} -Dsonar.java.binaries=target/classes -Dsonar.exclusions='**/*/test/**/*, **/*/acceptance-test/**/*, **/*.html'"
          }
        }
      }
    }
    
    stage('DependencyCheck'){
      steps{
        // Se agrega el argumento --disableYarnAudit para evitar warning, otra opción es instalar Yarn en Jenkins
        dependencyCheck additionalArguments: '--disableYarnAudit --format HTML', odcInstallation: 'DependencyCheck'
      }
    }
    
  }
}
