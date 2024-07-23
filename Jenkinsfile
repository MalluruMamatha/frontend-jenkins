pipeline{
    agent {
        label 'agent-1'
    }
    options{ /// using option we can specify the time that pipeline should execute 
    //(with in the time it get complets)
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
       
    }

    environment{

        def appversion = '' // variable declaration
        def nexusUrl = 'nexus.malluru.online:8081'
     
    }

   stages{

        stage('read version'){
            steps{
                script{
                    def packageJSON = readJSON file: 'package.json' // here we are reading the package.json file
                    appversion = packageJSON.version // in the package.json file we are getting the version
                    echo "application version is: $appversion"
                }
            }
        }

        // stage('install dependencies'){
        //     steps{
        //         sh """
        //          npm install
        //          ls -lt
        //          echo "application version is: $appversion"
        //         """
           
        //     }
        // }

                /// zip -r <filename.zip> <which file you want to zip> 
                // * --> means all files will include 
                // -x Jenkinsfile-----> means we are excluding the Jenkinsfile 

        stage('build'){
            steps{
                sh """
                zip -q -r frontend-${appversion}.zip * -x Jenkinsfile -x frontend-${appversion}.zip
                ls -ltr
                """
            }
        }
        stage('nexus artifact upload'){
            steps{
                script{
                     nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appversion}",
                        repository: "frontend",
                        credentialsId: 'nexus-authentication',
                        artifacts: [
                            [artifactId: "frontend",
                            classifier: '',
                            file: "frontend-" + "${appversion}" + '.zip',
                            type: 'zip']
                        ]
                    )
                }
            }
        }

        stage('deploy'){
            steps{
                script{

                    def params = [
                        string(name: 'appversion', value: "${appversion}")
                    ]
                   
                    build job: 'frontend-deploy', parameters: params, wait: false

   
        
                }
            }
        }
           
    }

    post{

        always{
            echo 'always say hello' 
            //deleteDir()  ////delete workspace when build is done
        }
        success{
            echo 'i will run when pipeline is success'

        }
        failure{
            echo 'i will run when pipeline is failure'

        }
    }
}



