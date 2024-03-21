@Library('devops-projects-shared-lib') _

pipeline{
    agent {
        label 'docker'
    }

    parameters {
        choice choices: ['create', 'delete'], description: 'Choose create or Delete', name: 'action'
        string defaultValue: 'gitops-demo', description: 'Name of the Image', name: 'ImageName'
        string defaultValue: 'v1', description: 'Tag of the Image', name: 'ImageTag'
        string defaultValue: 'csnkarthik', description: 'Name of the App', name: 'dockerHubUser'
    }
    
    environment {
        DOCKER_CREDENTIALS = credentials('docker_credentials')
    }

    stages{

        stage('Git Checkout'){            
            when { expression { params.action == 'create' } }
            steps {                
                // gitCheckout(
                //     branch: 'main',
                //     url: 'https://github.com/csnkarthik/GitOps-Project.git'
                // )
                checkout([$class: 'GitSCM', 
                          branches: [[name: '*/main']], 
                          doGenerateSubmoduleConfigurations: false,
                          extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'code']], 
                          submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/csnkarthik/GitOps-Project.git']]]) 
            }
        }

        stage('build and push'){  
            when { expression { params.action == 'create' } }
            steps
            {
                // build
                dockerBuild("${params.ImageName}", "${params.ImageTag}", "${params.dockerHubUser}");

                push
                dockerImagePush("${params.ImageName}", "${params.ImageTag}", "${params.dockerHubUser}");
            }            
        }        
    }
}