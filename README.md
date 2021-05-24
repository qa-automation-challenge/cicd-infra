# CI/CD training

## Skill Prerequisites
 * Git basics
 * Docker basics
 * Development basics (any kind)

## Software Prerequisites
 * Git (at least 2.X)
 * Docker (engine version at least 20.X.X) and Docker-compose (at leat 3.7) 
    * Configured to use at least, **4 CPUs and 6GB of ram**
 * Maven (at least 3.6.X)

## Hardware Minimum Requirements
 * 16GB Ram
 * Quad core CPU, ex: i7 8th gen
 * 2,4 GB for docker images + 20MB for maven dependencies

## Concepts to study prior to starting hands-on training: **(order matters)**
 * Git Branching - https://www.celfocus.com/beacon/git-branching-models/
 * Semantic Versioning - https://semver.org/
 * Static code analysis - https://istqbfoundation.wordpress.com/2017/09/18/static-analysis-by-tools/
 * CI/CD big picture - https://www.pluralsight.com/courses/continuous-integration-delivery-big-picture
 * Jenkins declarative pipelines - https://www.pluralsight.com/courses/using-declarative-jenkins-pipelines

## Hands-on Training
### Raising the infrastructure

The infrastructure for CI/CD will always be in a remote machine in the cloud, but for this training we will reproduce it in our local machines in order to have full access to all configurations needed. We will use docker-compose for this part but will not go in depth for the Docker technology details, as this is not the scope of this training.\
**Make sure your docker is configured to use at least, 4 CPUs and 6GB of ram**.

Raise the infrastructure by running:

```docker-compose up -d --build --force-recreate```

It's going to take a couple of minutes due to the size and configurations being done in the images.\
After the command displays that the containers are ready, wait at least 3-5 min before trying the next step.

The following infrastructure is now raised in your localhost:

| Application | URL                                     | User    | Password                   |
|:-----------|:----------------------------------------|:--------|:---------------------------|
| Jenkins    | [localhost:8080](http://localhost:8080) | No user | No password                |
| Sonarqube  | [localhost:9000](http://localhost:9000) | admin   | admin                      |
| Nexus      | [localhost:8081](http://localhost:8081) | admin   | Get from the container**   |

***execute ```docker container exec nexus cat nexus-data/admin.password```*

Open Sonarqube and Nexus service in you browser to configure new passwords as it is a required step for initial configuration.

You can check the details of the infrastructure by consulting the docker-compose.yaml file

Next, in order to use Jenkins & Sonarqube configure the following:

In Sonarqube:
 * Go to "Administration" -> "Security " -> "Users" and create a token for your user, **store it for later usage**.
 * Select "Configuration" -> "Webhooks" and create a webhook with "name" ```jenkins```, "url" ```http://jenkins:8080/sonarqube-webhook/``` and a secret word of your choice
 * Select "Configuration" -> "General" and set the "Server base URL" to ```http://sonarqube:9000```

In Jenkins:
 * Go to "Manage Jenkins" -> "Configure System"
 * Under "Jenkins Location" -> "Jenkins URL" input ```http://jenkins:8080/```
 * Under "SonarQube servers" set the "Name" ```Sonarqube```, "Server URL" ```http://sonarqube:9000``` and add a Server authentication token with the token previously stored and ID ```sonar-token```

### Creating your first pipeline

We have prepared one sample project for this session using Maven.

Maven - [https://app.celfocus.com/gitlab/QA/training/qa-training-cicd-mvn](https://app.celfocus.com/gitlab/QA/training/qa-training-cicd-mvn) \

Go ahead and fork it to your own repository.\
Now, let's use the feature branching strategy. The first step is to create a new branch named *CICD-001* from develop branch.

Notice how in the Jenkins directory you can find a "Jenkinsfile", this is where we will describe how and what our CI/CD pipeline will do.

In Jenkins ( [localhost:8080](http://localhost:8080) ) :
 * Click on "Create a job"
 * Enter a new name ex: qa-training-cicd
 * Select "Multibranch Pipeline"
 * Under "Branch Sources", select "Add source" -> "Git"
 * Enter your fork repository name ex: https://app.celfocus.com/gitlab/nbxxxxx/qa-training-cicd-mvn.git
 * Under "Credentials", select "Add" -> "Jenkins"
 * In the "Add Credentials" window, fill in your "Username", "Password" and in "ID" fill in "git-credentials". Then press the "Add" button
 * Under "Credentials", select your newly added credential from the dropdown
 * Under "Build Configuration" -> "Script Path", write "jenkins/Jenkinsfile" as this is the location of our Jenkinsfile within the repository
 * Click on "Save"

The pipeline will now run automatically, and you should see "Finished: SUCCESS" at the end of the log.

Congratulations, you have now created a pipeline for this CI/CD training session.

# Exercises
### Filling in the pipeline stages

Your pipeline is currently failing. The main objective of this session is to build a pipeline with the correct stages to do the following:
 1. Unit tests in all branches
 2. Checkstyle in all branches
 3. Sonarqube analysis all branches
 4. Merge feature branches with development branch
 5. Merge development branch with master branch
 6. Git tag code versions with semantic versioning
 7. Deploy artifacts to Nexus
