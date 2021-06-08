node ('societe-vm'){
 
    stage('SCM Checkout'){
        git credentialsId: 'GIT_CREDENTIALS', url:  'https://github.com/Saswat93/Code-Kata.git',branch: 'main'
    }
  
    stage ('Code Coverage'){
       
                echo "========Sonar Analysis Started========="
                sh '''
                "E:\\sonar-scanner-cli-4.2.0.1873-windows\\sonar-scanner-4.2.0.1873-windows\\bin\\sonar-scanner.bat"
                '''
            }
        }

      stage("Build"){
      def mavenHome =  tool name: "Maven-3.6.1", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 


   stage ("Test"){
	     
	         junit '**/target/surefire-reports/TEST-*.xml'
	         archiveArtifacts '*'
     }
    
    stage('Build Docker Image'){
        sh 'docker build -t saswatpattnaik21/myimage .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'DOCKER_PASSWORD', variable: 'DOCKER_PASSWORD')]) {
          sh "docker login -u saswatpattnaik21 -p ${DOCKER_PASSWORD}"
        }
        sh 'docker push saswatpattnaik21/myimage'
     }
     
     stage("Deploy Cluster"){
       kubernetesDeploy(
         configs: 'deployment.yml', 
         kubeconfigId: 'KUBE_Config',
         enableConfigSubstitution: true
        )
     }

    stage ("Notify"){
       emailext (
       subject: "Job '${env.JOB_NAME} ${env.BUILD_NUMBER}'",
       body: """<p>Check console output at <a href="${env.BUILD_URL}">${env.JOB_NAME}</a></p>""",
       to: "saswatpattnaik993@gmail.com",
       from: "saswatpattnaik21@gmail.com"
)
     }
