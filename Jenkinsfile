// Jenkinsfile
String credentialsId = 'AWS_FAROOQ'

parameters {
      string(name: 'SE_PROJECT_TAG', defaultValue: 'nirone', description: 'pick the product tag. e.g. nirone')
      string(name: 'ENVIRONMENT', defaultValue: 'Develop', description: 'pick the product tag. e.g. nirone')
      //choice(name: 'ENVIRONMENT', choices: ['Test', 'Staging', 'Production', 'Development', 'Hotfix'], description: 'Pick the target environment.')
      //string(name: 'AWS_CRED', defaultValue: 'AWS_DEV', description: 'Enter your AWS credentials id in Jenkins.')
      string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'Enter your target AWS region.')
      //booleanParam(name: 'DESTROY', defaultValue: false, description: 'Destroying?! note: make sure to have backup!')
      //string(name: 'TEST_DURATION', defaultValue: '10', description: 'Enter the duration you want the perfomance stage to run.')
      //choice(name: 'DEPLOYMENT_MODE', choices: ['Amazon AMI', 'Fresh'], description: 'Fresh deployment or from AWS AMI?.')
      //booleanParam(name: 'LIGHTWEIGHT', defaultValue: false, description: 'Setting the lightweight deployment, ignoring Terraform stages.')
      //booleanParam(name: 'UPDATE', defaultValue: false, description: 'Updating the containers and/or configs.')
      //booleanParam(name: 'WEBUI', defaultValue: false, description: 'Updating WEB UI Containers')
  }

try {
  stage('checkout') {
    node {
      cleanWs()
      checkout scm
    }
  }

  parameters {
      string(name: 'SE_PROJECT_TAG', defaultValue: 'nirone', description: 'pick the product tag. e.g. nirone')
      string(name: 'ENVIRONMENT', defaultValue: 'Develop', description: 'pick the product tag. e.g. nirone')
      //choice(name: 'ENVIRONMENT', choices: ['Test', 'Staging', 'Production', 'Development', 'Hotfix'], description: 'Pick the target environment.')
      //string(name: 'AWS_CRED', defaultValue: 'AWS_DEV', description: 'Enter your AWS credentials id in Jenkins.')
      string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'Enter your target AWS region.')
      //booleanParam(name: 'DESTROY', defaultValue: false, description: 'Destroying?! note: make sure to have backup!')
      //string(name: 'TEST_DURATION', defaultValue: '10', description: 'Enter the duration you want the perfomance stage to run.')
      //choice(name: 'DEPLOYMENT_MODE', choices: ['Amazon AMI', 'Fresh'], description: 'Fresh deployment or from AWS AMI?.')
      //booleanParam(name: 'LIGHTWEIGHT', defaultValue: false, description: 'Setting the lightweight deployment, ignoring Terraform stages.')
      //booleanParam(name: 'UPDATE', defaultValue: false, description: 'Updating the containers and/or configs.')
      //booleanParam(name: 'WEBUI', defaultValue: false, description: 'Updating WEB UI Containers')
  }

  // Run terraform init
  stage('init') {
    node {
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        ansiColor('xterm') {
          sh 'terraform init -backend=true -input=false -backend-config="key=us-east-1/nirone/develop/terraform.tfstate"'
        }
      }
    }
  }

  // Run terraform plan
  stage('plan') {
    node {
      withCredentials([[
        $class: 'AmazonWebServicesCredentialsBinding',
        credentialsId: credentialsId,
        accessKeyVariable: 'AWS_ACCESS_KEY_ID',
        secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
      ]]) {
        ansiColor('xterm') {
          sh 'terraform plan'
        }
      }
    }
  }

  if (env.BRANCH_NAME == 'master') {

    // Run terraform apply
    stage('apply') {
      node {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh 'terraform apply -auto-approve'
          }
        }
      }
    }

    // Run terraform show
    stage('show') {
      node {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: credentialsId,
          accessKeyVariable: 'AWS_ACCESS_KEY_ID',
          secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) {
          ansiColor('xterm') {
            sh 'terraform show'
          }
        }
      }
    }
  }
  currentBuild.result = 'SUCCESS'
}
catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException flowError) {
  currentBuild.result = 'ABORTED'
}
catch (err) {
  currentBuild.result = 'FAILURE'
  throw err
}
finally {
  if (currentBuild.result == 'SUCCESS') {
    currentBuild.result = 'SUCCESS'
  }
}
