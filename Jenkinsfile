//Jenkinsfile (Declarative Pipeline)
pipeline {
    agent { label 'master' }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
        AWS_DEFAULT_REGION = credentials('jenkins-aws-default-region')
        TF_WARN_OUTPUT_ERRORS = 1
    }
    //parameters {
      //  string(name: 'TFWORKSPACE', defaultValue: 'xxxx', description: 'Enter Terraform workspace')
  //  }

    stages {
        stage('Checkout') {
            agent { label 'master' }
            steps {
                git branch: 'master', credentialsId: '8377b579-f079-40d7-b4fb-adca444343d0', url: 'https://github.anaplan.com/terraform/tf_popidol.git'
            }
        }
        stage('Prep') {
            agent { label 'master' }
            steps {
                withCredentials([file(credentialsId: 'lucskytfvars', variable: 'SECRETFILE')]) {
                      sh '''
                          set +x
                          cat  "${SECRETFILE}" > lucsky.tfvars
                      '''
                }
                withCredentials([file(credentialsId: 'cheflibatapem', variable: 'CHEFLIBATAPEMFILE')]) {
                      sh '''
                          set +x
                          cat  "${CHEFLIBATAPEMFILE}" > /tmp/libata.pem
                      '''
                }
                withCredentials([file(credentialsId: 'chefdonotdelete', variable: 'CHEFDONOTDELETEFILE')]) {
                      sh '''
                          set +x
                          cat  "${CHEFDONOTDELETEFILE}" > /tmp/do-not-delete
                      '''
                }
                withCredentials([file(credentialsId: 'sshanaplanpem', variable: 'SSHANAPLANPEMFILE')]) {
                      sh '''
                          set +x
                          cat  "${SSHANAPLANPEMFILE}" > /tmp/anaplan.pem
                      '''
                }
                withCredentials([file(credentialsId: 'sshidrsainsecure', variable: 'SSHIDRSAINSECUREFILE')]) {
                      sh '''
                          set +x
                          cat  "${SSHIDRSAINSECUREFILE}" > /tmp/id_rsa.insecure
                      '''
                }
            }
        }
        stage('Apply') {
            agent { label 'master' }
            steps {
                wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']) {

                    sh '''
                        /opt/terraform init
                        # /opt/terraform workspace new lucsky
                        /opt/terraform workspace select lucsky
                        /opt/terraform workspace show
                        /opt/terraform apply  -input=false -auto-approve -var-file=lucsky.tfvars
                    '''
                }
            }
            post {
              success {

                emailext (
                    subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                    body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                      <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                    recipientProviders: [[$class: 'DevelopersRecipientProvider']]
                  )
              }

              failure {
                emailext (
                    subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                    body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                      <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                    recipientProviders: [[$class: 'DevelopersRecipientProvider']]
                  )
              }
            }
        }
        stage('Test') {
           agent { label 'master' }
           steps {
                 sh '''
                    sleep 20
                 '''

           }
           post {
             success {

               emailext (
                   subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                   body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                     <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                   recipientProviders: [[$class: 'DevelopersRecipientProvider']]
                 )
             }

             failure {
               emailext (
                   subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                   body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
                     <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
                   recipientProviders: [[$class: 'DevelopersRecipientProvider']]
                 )
             }
           }

        }

    }
}
