podTemplate(label: '12-mall',
	containers: [
        containerTemplate(name: 'git', image: 'alpine/git', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'maven', image: 'maven:alpine', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat')
  ],
  volumes: [ 
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), 
  ]
) {
    node('12-mall') { 
        def appImage
        
        stage('Checkout'){
            container('git'){
                checkout scm
            }
        }
         stage('Compile & Test'){
            container('maven'){
                script {
                    sh 'mvn package'
                }
            }
        }
        stage('Build'){
            container('docker'){
                script {
                    appImage = docker.build("052937454741.dkr.ecr.ap-northeast-2.amazonaws.com/"+"${env.JOB_NAME}")
                }
            }
        }
        stage('Push'){
            container('docker'){
                script {
                    docker.withRegistry("https://052937454741.dkr.ecr.ap-northeast-2.amazonaws.com/", "ecr:ap-northeast-2:ecr-cred"){
                        appImage.push("${env.BUILD_NUMBER}")
                        appImage.push("latest")
                    }
                }
            }
        }      
        stage('Deploy'){
            container('git'){
		        kubernetesDeploy(kubeconfigId: 'kube-cred', 	// REQUIRED
	                 configs: '**/kubernetes/*.yaml',           // REQUIRED
	                 enableConfigSubstitution: true
	            )
            }
         }
    }
}
