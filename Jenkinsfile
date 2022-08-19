pipeline {
    agent any

    parameters {
        booleanParam(name: "PULL", defaultValue: true, description: "Pull source code or not.")
        booleanParam(name: "BUILD", defaultValue: true, description: "Build source code & docker image or not.")   
        booleanParam(name: "TAG", defaultValue: true, description: "Tag the builded docker image or not.")
        booleanParam(name: "PUSH", defaultValue: true, description: "Push the builded docker image to docker hub or not.")  
        booleanParam(name: "DEPLOY", defaultValue: true, description: "Deploy the builded docker image to service or not.")  
        string(name: "OLD_BUILD_NO", defaultValue: '', trim: true, description: "Default is null. If you want to deploy older docker image, enter older build number.")        
        
//        string(name: "TEST_STRING", defaultValue: "ssbostan", trim: true, description: "Sample string parameter")
//        text(name: "TEST_TEXT", defaultValue: "Jenkins Pipeline Tutorial", description: "Sample multi-line text parameter")
//        password(name: "TEST_PASSWORD", defaultValue: "SECRET", description: "Sample password parameter")
//        choice(name: "TEST_CHOICE", choices: ["production", "staging", "development"], description: "Sample multi-choice parameter")
    }
    
    environment {
        ARTIFACTORY = credentials('docker-hub')
        DOCKER_USER_ID = "${ARTIFACTORY_USR}"
        DOCKER_USER_PASSWORD = "${ARTIFACTORY_PSW}"
        DEPLOY_NUMBER = ${params.OLD_BUILD_NO} //'32' //"${params.OLD_BUILD_NO == '' ? '${BUILD_NUMBER}' : '${params.OLD_BUILD_NO}'}"   
    }    
 
        stages {
            stage("PULL") {
                when {
                    expression{ params.PULL==true } 
                }
                steps {
                    echo "# Pull stage."
                    git 'https://github.com/ahnlabtest/jenkins_flask.git'
                }
            }
            stage("BUILD") {
                when {
                    expression{ params.BUILD==true }
                }
                steps {
                    echo "# Build stage."
                    sh(script: 'docker-compose build app')
                }
            }
            stage("TAG") {
                when {
                    expression{ params.TAG==true }
                }
                steps {
                    echo "# TAG stage."
                    sh(script: '''docker tag ${DOCKER_USER_ID}/flask \
                        ${DOCKER_USER_ID}/flask:${BUILD_NUMBER}''')
                }
            }            
            stage("PUSH") {
                when {
                    expression{ params.PUSH==true }
                }
                steps {
                    echo "# PUSH stage."
                    sh(script: 'docker login -u ${DOCKER_USER_ID} -p ${DOCKER_USER_PASSWORD}')
                    sh(script: 'docker push ${DOCKER_USER_ID}/flask:${BUILD_NUMBER}')
                    sh(script: 'docker push ${DOCKER_USER_ID}/flask:latest')
                    sh(script: 'docker logout')
                }
            }                    
            stage("DEPLOY") {
                when {
                    expression{ params.DEPLOY==true }
                }
                steps {
                    echo "# DEPLOY stage."
                    echo '${DEPLOY_NUMBER}'
                    script{
                            sh 'echo ${DEPLOY_NUMBER}'
                    }
                }
            }                  
        }
}
