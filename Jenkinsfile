node('dockerhost') {
  def appName = 'flask'
  def imageTag = "cloudyuga/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

  checkout scm

  stage 'Build image'
  sh("docker build -t ${imageTag} .")

  //stage 'Run Go tests'
  //sh("docker run ${imageTag} go test")

  stage 'Push image to registry'
  sh("docker push ${imageTag}")

  stage "Deploy Application"
  switch (env.BRANCH_NAME) {
    // Roll out to staging
    case "staging":
        sh("docker run -d --name=staging_flask -p 5001:5000 ${imageTag}")
        break

    // Roll out to production
    case "master":
        input 'are you sure ?'
        sh("docker run -d --name=production_flask -p 5000:5000 ${imageTag}")
        break

    // Roll out a dev environment
    default:
        // Create namespace if it doesn't exist
        sh("docker ps")
  }
}
