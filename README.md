# Solution for Part A
- I have link with all success stories case studies. You can see and choose one and do with it
- Link : https://aws.amazon.com/solutions/case-studies/browse-customer-success-stories/

- You can use Filter by to choose :
`It's maybe choose`
    + Location : 
    + Industry : It's up to you
    + ...
    + Services Used : Maybe use AWS CodeBuild , CodeCommit, CodeDeploy, CodePipeline {Because it relate our project part B}. But it's up to you

# Solution for Part B
## 1. Question of fundamentals
1. What is AWS?
Answer: AWS stands for Amazon Web Services. It's a subsidiary of Amazon providing on-demand cloud computing platforms and APIs to individuals, companies, and governments, on a metered pay-as-you-go basis.

2. What are AWS CodeCommit, AWS CodeBuild, AWS CodeDeploy, and AWS CodePipeline?
Answer:

- AWS CodeCommit is a fully managed source control service that hosts secure Git-based repositories.
- AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy.
- AWS CodeDeploy automates software deployments to a variety of compute services such as Amazon EC2, AWS Fargate, AWS Lambda, and on-premises servers.
- AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates.
3. What is a CI/CD Pipeline, and how does it work with the tools mentioned above?
Answer:
- CI/CD stands for Continuous Integration and Continuous Deployment/Delivery. It's a software development practice where code changes are automatically built, tested, and prepared for a release to production.
With the AWS tools:

- Changes are first stored in AWS CodeCommit.
- They are then automatically built and tested with AWS CodeBuild.
- AWS CodeDeploy takes these changes and deploys them to specified environments.
- AWS CodePipeline orchestrates the entire process from commit to deploy, integrating all the mentioned services into a streamlined pipeline.
4. Why is a CI/CD Pipeline necessary?
Answer: A CI/CD Pipeline is essential because it automates the software delivery process. This automation ensures faster, consistent, and reliable deliveries, reduces manual errors, provides quicker feedback loops, and enables more frequent releases of quality software.
5. Describe the software project you participated in (CI/CD Pipeline with AWS project).
Answer: I was involved in a project where we implemented a CI/CD Pipeline using AWS services. The goal was to automate the software delivery process, ensuring faster and more reliable releases. We utilized AWS CodeCommit for source code control, AWS CodeBuild for automated builds and testing, AWS CodeDeploy for deploying to various environments, and AWS CodePipeline to orchestrate the entire process seamlessly.

6. Describe a situation where you had to implement it in real-life.
Answer: In a previous role, our team was managing a rapidly growing web application. With frequent releases, manual deployments became error-prone and time-consuming. We needed a more consistent and efficient process. Implementing the CI/CD pipeline using AWS tools allowed us to ensure every code change went through standardized tests and deployments, reducing errors and speeding up our release cycle. This real-life implementation was pivotal in maintaining the application's quality and reliability while adapting to the increasing demand.

## Note : You can use it to refer.Let adjust for you
--------------------------------------------------------------
## 2. Project Step by Step
`In this project you can find any simple web to add for this solution. I added for you 2 web project`
### Note : I added feature about git branch for you . About git branch :
7. What is a Git branch?
Answer: A Git branch is a pointer to a sequence of commits, representing an independent line of development in a Git repository. It allows developers to diverge from the main line of development and work separately without affecting the main codebase. Once the feature or fix on the branch is complete, it can be merged back into the main branch or any other branch.

8. How is a Git branch applied in real-life projects?
Answer: In real-life projects, Git branches are used to manage different development tasks concurrently. For instance, developers often create separate branches for new features, bug fixes, or experiments. This ensures that the main codebase (often the "master" or "main" branch) remains stable. Once development on a branch is completed and tested, it is merged back into the main branch, making the changes available to everyone. Using branches helps teams maintain a clean and functional main codebase while allowing for parallel development efforts.

## Start ---->
# Part 1
## 1. AWS Codecommit 
- Create repository : demo-app 
- IAM
  + Add user
  + enable console access
    > Then generate credentials
- Clone URL : in terminal VScode ( and add username and password )
- Add permission : AWSCodecommitPowerUser
- Test commit code , and change branch
```
git add .
git commit -m ""
git push origin master
```
- And then change code and push with branch dev
```
git checkout -b dev
# change code , and remember save
git add .
git commit -m ""
git push origin dev
# if dev >> then we need merge
```
- Create Pull Request in Codecommit
  + overview
  + Then Merge the code

## 2. Create builds on AWS CodeBuild
- Name : demo-app-build-test
- Source : Choose : AWS Codecommit
- Repository
- Branch : master
- Managed image
- OS : ubuntu
- Standard
- Create a new service role
- Choose Buildspec.yml
  + [Link tutorial buildspec.yml](https://docs.aws.amazon.com/codebuild/latest/userguide/getting-started-cli-create-build-spec.html)
- no Cloudwatch

## 3. Artifacts in AWS 
Artifacts are the output or result of the build process and typically represent the compiled or packaged version of your application or software.
- Choose S3
- Create S3 bucket
- create folder in s3 bucket
- Copy path on Path in Artifacts + artifacts.zip
- Then we start build , we will have artifact.zip
- + Well can edit again artifcts
    
# Part 2 Hands-on Tutorial
## 1. Create Application in CodeDeploy
- Create Application
- Choose compute platform : EC2/On premises

## 2. Create Deployment Group
- Sevice Role {Create new Role}
  + AmazonEC2FullAccess
  + AmazonEC2RoleforAWSCodeDeploy
  + AmazonS3FullAccess
  + AWSCodeDeployFullAccess
  + AWSCodeDeployRole
  + AmazonEC2RoleforAWSCodeDeployLimit...
- Enviroment : Amazon EC2 instances
  + Key   : Name
  + Value : {Create a new EC2 instances, remember create key-value} 
- Install AWS CodeDeploy Agent : Never
- No enable Balance
## 3. Setting up Agent for CodeDeploy
[Link tutorial](https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install-ubuntu.html)
Create a shell script {install.sh} with the below contents and run it
```
#!/bin/bash 
# This installs the CodeDeploy agent and its prerequisites on Ubuntu 22.04.  
sudo apt-get update 
sudo apt-get install ruby-full ruby-webrick wget -y 
cd /tmp 
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/releases/codedeploy-agent_1.3.2-1902_all.deb 
mkdir codedeploy-agent_1.3.2-1902_ubuntu22 
dpkg-deb -R codedeploy-agent_1.3.2-1902_all.deb codedeploy-agent_1.3.2-1902_ubuntu22 
sed 's/Depends:.*/Depends:ruby3.0/' -i ./codedeploy-agent_1.3.2-1902_ubuntu22/DEBIAN/control 
dpkg-deb -b codedeploy-agent_1.3.2-1902_ubuntu22/ 
sudo dpkg -i codedeploy-agent_1.3.2-1902_ubuntu22.deb 
systemctl list-units --type=service | grep codedeploy 
sudo service codedeploy-agent status
```
Then
```
bash install.sh
```
## 4. Create code for web , buildspec.yml and deployspec.yml
- [Link tutorial deployspec.yml](https://docs.aws.amazon.com/codedeploy/latest/userguide/tutorials-wordpress-configure-content.html)
- git clone from codecommit
- Then
```
git add .
git commit -m ""
```
- Then build in codebuild

## 5. Create deployment with code in S3
- {Remember add code in S3}
- Add path of demo_app.zip
- Permisions policies
  + AmazonEC2FullAccess
  + AmazonS3FullAccess
  + AmazonCodeDeployFullAccess
- Modify IAM role on EC2 with this role

## 6. Create Pipeline
- Name
- New service role
- Source : CodeCommit
  + Repository name : demo-app
  + Branch name : master
  + Change detection : AWS Codepipeline
- Builder provider :
- Deploy : AWS Codedeploy
- Overview
Test by
```
git add .
git commit -m "test"
git push origin master
```
* Note LF and CRLF above terminal
* Artifact.zip must have appspec.yml , and buildspec.yml in root folder ( on lap and on aws )
