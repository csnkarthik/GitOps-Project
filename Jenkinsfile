@Library('devops-projects-shared-lib') _

pipeline{
    agent {
        label 'docker'
    }

    parameters {
        choice choices: ['create', 'delete'], description: 'Choose create or Delete', name: 'action'
        //string defaultValue: 'gitops-demo', description: 'Name of the Image', name: 'ImageName'
        //string defaultValue: 'v1', description: 'Tag of the Image', name: 'ImageTag'
        //string defaultValue: 'csnkarthik', description: 'Name of the App', name: 'dockerHubUser'
    }
    
    environment {
        DOCKER_CREDENTIALS = credentials('docker_credentials')

        GIT_CREDENTIALS = credentials('GitOps-Demo-Manifest')
    }

    stages{

        stage('Git Checkout'){            
            when { expression { params.action == 'create' } }
            steps {                
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
                dockerBuild("gitops-demo", "${BUILD_NUMBER}", "csnkarthik");

                dockerImagePush("gitops-demo", "${BUILD_NUMBER}", "csnkarthik");
            }            
        }    

        stage('update manifest'){  
            when { expression { params.action == 'create' } }
            steps
            {
               sshagent(['GitOps-Demo-Manifest']) {
                   script{

                        sh """
                            git config --global user.email karthik.aspx.cs@gmail.com
                            git config --global user.name csnkarthik

                            git -C GitOps-Demo-Manifest pull || git clone git@github.com:csnkarthik/GitOps-Demo-Manifest.git                             
                            cd GitOps-Demo-Manifest

                            sed -i "s/gitops-demo:.*/gitops-demo:${BUILD_NUMBER}/g" values.yaml
                        
                            git add .
                            git commit -m "Updated Image Tag: ${BUILD_NUMBER}"   
                            git push git@github.com:csnkarthik/GitOps-Demo-Manifest.git        
                        
                        """
                    }
               }
            }            
        }       
    }
}