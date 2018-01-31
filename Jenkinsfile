#!groovy

// Setup the AWS Credentials
wrappers {
  credentialsBinding {
    amazonWebServicesCredentialsBinding {
      accessKeyVariable("AWS_ACCESS_KEY_ID")
      secretKeyVariable("AWS_SECRET_ACCESS_KEY")
      credentialsId("47536ade-f5cb-4a94-b5ab-3437ba578de5")
    }
  }
}

node {
    // Get the Terraform tool.
    def tfHome = tool name: 'Terraform', type: 'com.cloudbees.jenkins.plugins.customtools.CustomTool'
    env.PATH = "${tfHome}:${env.PATH}"

  stage ('Checkout') {
    git url: 'git@github.com:ibata/devOps.git'
  }

  stage ('Terraform Plan') {
    sh 'terraform plan -no-color -out=create.tfplan'
  }

  // Optional wait for approval
  input 'Deploy stack?'

  stage ('Terraform Apply') {
    sh 'terraform apply -no-color create.tfplan'
  }

  stage ('Post Run Tests') {
    echo "Insert your infrastructure test of choice and/or application validation here."
    sleep 2
    sh 'terraform show'
  }

  stage ('Notification') {
    mail from: "jenkins@mycompany.com",
         to: "devopsteam@mycompany.com",
         subject: "Terraform build complete",
         body: "Jenkins job ${env.JOB_NAME} - build ${env.BUILD_NUMBER} complete"
  }
}
