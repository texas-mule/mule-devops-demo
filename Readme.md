# Deploy with Maven
>mvn package deploy -Duid="AnypointUsername" -Dpwd="AnypointPassword" -Psandbox

# Continuous Integration

>"Continuous Integration is a software development practice where members of a team integrate their work frequently, usually each person integrates at least daily - leading to multiple integrations per day. Each integration is verified by an automated build (including test) to detect integration errors as quickly as possible. Many teams find that this approach leads to significantly reduced integration problems and allows a team to develop cohesive software more rapidly." 
<div style="text-align: right">~Martin Fowler</div>

DevOps culture aims to minimize the barriers between developers and operations as a whole, but it also streamlines the development process itself through Continuous Integration (CI). 

When working with several developers on a single code repository, safely integrating many changes from each developer is a challenge. Too few integrations and the code base may introduce bugs or become broken without any clear insight into when and how it happened. Each developer should not only be responsible for maintaining up-to-date code on their development environments, but also be responsible for regularly committing new code that passes local unit tests.

## Automated builds
As developers integrate new code, an automated build on a server separate from each of their development environments should trigger. After the build is pulled from the repository, compiled, and tested automatically, the developers should be notified of any and all issues or errors introduced by recent commits. This ensures any problems introduced into the repository are quickly highlighted before becoming buried by the next round of commits.

### Scripts
On a build server, the automated process may be as simple as a scheduled script which pulls the latest code from the repository, builds a new artifact from the changes, and logs and reports on the status of any compiling errors or test failures. The following is an example for a Linux server:

#### `build.sh`
```bash
#!/bin/bash
git pull https://your-repository.git
cd your-repository/
mvn clean package
#Mail log output from build to developers
```
Afterwards, register this script as a `cron` job. This will run the script at a determined interval. The script should be made executable, and the server should have all necessary tools installed.

### CI/CD Tools
Advanced tools like Jenkins, Circle-CI, Travis-CI, and many more simplify the automation process. Whether hosted on your own build server or as a cloud service, they can coordinate SCM, build, deployment, and messaging tools through a user-friendly web interface and have access to a large community of plugins or excellent integration with repository services like GitHub.

# Continuous Delivery
>The essence of my philosophy to software delivery is to build software so that it is always in a state where it could be put into production. We call this Continuous Delivery because we are continuously running a deployment pipeline that tests if this software is in a state to be delivered.
<div style="text-align: right">~Martin Fowler</div>

Just as a team of developers use continuous, automated builds to integrate new changes to their code base rapidly, efficiently, and safely, an operations team benefits from automating a rapid delivery process of that build to the various teams and servers in preparation for deployment.

## Pipelines
The process of moving the build (or rebuilding from source) beyond the initial Continuous Integration build is called Continuous Delivery (CD), and the solution that implements this process is known as the deployment (or build) pipeline.

The first CI bulid, the build triggered by a commit or series of commits by developers, should be done quickly to satisfy CI requirements: quick unit tests and error-free compilations are enough. But afterwards the code base should be made available to other teams to run more extensive testing and performance monitoring.

An example two stage deployment pipeline would have the first stage do the CI compilation and unit testing while the second stage builds on a separate testing server to handle integration and end-to-end behavior testing. Further stages can be added at will, triggered at their own intervals.

Just like with CI, simple scripts can automate the process of building, testing, or deploying to staging servers. Tools such as Jenkins also simplify the process of building pipelines and can coordinate multiple servers with ease.

# Jenkins
Forked from Sun's Hudson project after it was acquired by Oracle, Jenkins is a popular open-source CI/CD tool used throughout the industry to create and manage build pipelines. Jenkins is distributed as a Java `war` package and can be deployed to a Tomcat server or run as a self-executing `war`. The Jenkins dashboard is then accessible through a web application.

## Setup
When Jenkins first runs, it creates a `.jenkins` directory where pipelines and project workspaces will be managed, along with an installation directory elsewhere for managing tools, plugins, and users.

Once Jenkins is up and running, the dashboard can be found at `http://localhost:8080` by default. The Unlock Jenkins page will prevent further access until the autogenerated password is copied from the file location specified on the page or on the console log output when running Jenkins as an executable `war`.

Once unlocked, Jenkins can download several popular plugins and help configure the admin account.

## Plugins
The popularity of Jenkins is in part thanks to its rich plugin community offering many helpful tools for a variety of build actions. On initial setup a variety of popular plugins come bundled with Jenkins and can be installed individually. More plugins can be discovered and installed (or removed).

## Global Tools
Jenkins runs its processes as a special user on its host operating system. In order to work with a variety of tools, it must install and configure permissions for these tools, ranging from command line programs such as Git or Maven to authentication of external services like GitHub or Slack. If certain tools are missing, Jenkins can be configured to automatically install them during a build.

## Users
A default admin account is registered during initial setup. If this admin account creation is skipped, the password will be the autogenerated password used to unlock Jenkins. Other accounts can be created, and permissions can be set by the admin using the security matrix. This is useful for restricting access to your pipelines to developers who may not require it.

## Jobs
A pipeline is defined through a Jenkins Job, a project created and monitored on the Jenkins dashboard. Jobs can be structured in multiple ways using a variety of archetypes. Each job creates a folder with a workspace directory where a bulid can be isolated from other jobs. A job is configured through a graphical wizard interface or programmatically using a Jenkinsfile script (written in Groovy, a JVM scripting language).

Besides the build itself, various pre-build and post-build stages can be defined in order to set environment variables, prepare build tools, poll SCM repositories, and log messages to email or other messaging services like Slack.

## Agents
Jenkins jobs normally run on the master node, the server the Jenkins instance defining the pipeline shares. But distributed builds are possible through a Jenkins Agent, a separate node hosted on a different server and linked to a master node through SSH. A Jenkins master with one or more agents can run a build on these agents thereby freeing up resources on the master for other jobs.

# Example Plugin use: GitHub Hooks
Rather than poll a GitHub repository at regular intervals, Jenkins can be configured to listen to the repository for any updates, then trigger a build.

## On GitHub
On a repository, under "Settings" and "Integrations and Services", the Jenkins (GitHub plugin) service can be added and configured using  `http://<your public DNS>:8080/jenkins/github-webhook/` as the Jenkins hook URL. "Active" should be checked.

Under the user's profile, "Settings", then "Personal access tokens" under "Developer settings", a user access token can be generated and assigned a repository scope. The generated token, an alphanumeric string pattern, can be copied over to Jenkins.

## On Jenkins
In "Manage Jenkins", "Global Tool configuration", The path to Git should be configured. Then Under "Configure System", then "Github", and "Add GitHub Server", the hook can be added. The API URL should be `https://api.github.com` while in "Credentials", then "Add/Jenkins" and "Kind", the "secret text" should be the generated token copied from GitHub. "Test connection" should display a message saying, "Credentials verified for user `<your github name>`... etc".

Then while configuring a project job, "Git" should be chosen under "Source Code Management" specifying the repository URL and branch to build from. Afterwards the "GitHub hook trigger for GITScm polling" under "Build Triggers" should enable the feature for the pipeline.
