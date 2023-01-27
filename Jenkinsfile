pipeline {
    agent any 
    tools { 
        maven 'Maven' 
      
    }
stages { 
     
 stage('Preparation') { 
    steps {
// for display purpose

      // Get some code from a GitHub repository

        git 'https://github.com/paragdodewar/GOL-Repo-1.git'

      // Get the Maven tool.
     
 // ** NOTE: This 'M3' Maven tool must be configured
 
     // **       in the global configuration.   
     }
   }

   stage('Build') {
       steps {
       // Run the maven build

      //if (isUnix()) {
         sh 'mvn -Dmaven.test.failure.ignore=true install'
      //} 
      //else {
      //   bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
       }
//}
   }
 
  stage('Unit Test Results') {
      steps {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.war'
      }
 }
  stage('Sonarqube') {
  //  environment {
     //   scannerHome = tool 'sonarqube'
  //  }
    steps {
        withSonarQubeEnv('sonarqube') {
           // sh "${scannerHome}/bin/sonar-scanner"
            sh 'mvn sonar:sonar -f pom.xml'
        }
    //    timeout(time: 10, unit: 'MINUTES') {
    //      waitForQualityGate abortPipeline: true
    //    }
    }
}
     stage('Artifact upload') {
      steps {
            //nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
            nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'gameoflife-web/target/gameoflife.war']], mavenCoordinate: [artifactId: 'gameoflife', groupId: 'com.wakaleo.gameoflife', packaging: 'war', version: '$BUILD_NUMBER']]]
      }
 }
    
        stage('Update Artifact Version') {
      steps {
        sh label: '', script: '''sed -i s/artifactversion/$BUILD_NUMBER/ deploy-withinfra.yml'''
      }
 }
    
    stage('Deploy War') {
      steps {
          print("Deployment")
          //deploy adapters: [tomcat8(credentialsId: 'tomcat-cred', path: '', url: 'http://18.220.134.203:8080/')], contextPath: null, war: '**/*.war'
          //sh label: '', script: 'ansible-playbook deploy-withinfra.yml'
          sh label: '', script: 'ansible-playbook deploy.yml'
      }
 }
}
post {
        success {
            archiveArtifacts 'gameoflife-web/target/*.war'
        }
        failure {
            mail to:"raknas000@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build failed"
        }
    }       
}
