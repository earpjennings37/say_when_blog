# 1. Run Deployment in Jenkins
- Source code forked

![alt text](image.png)

a. Jenkins:
- Setup Jenkins (Github access token, Docker Hub, & KubeConfig)

![alt text](image-1.png)

b. Github:
- Generate access token

c. DockerHub
- Does NOT generate access token

![alt text](image-2.png)

# 2. Add Canary to Pipeline to run Deployment

a. Create Jenkins Project
- Multi-Branch Pipeline
- Github username
- Owner & forked repository
- Provided an option for URL, select deprecated visualization

![alt text](image-3.png)

![alt text](image-4.png)

# 3. Canary Template:
- Pay Attention to track, spec, selector, & port

![alt text](image-5.png)

# 4. Add Jenkinsfile to Canary Stage"
- Between Docker Push & DeployToProduction
- We add CanaryDeployment stage!

# 5. Modify Productions Deployment Stage:

![alt text](image-6.png)