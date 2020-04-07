# Jenkins

## Recommended Plugins

* Ant Plugin
* Credentials Binding Plugin
* LDAP Plugin
* Pipeline: Stage View Plugin
* Pipeline
* OWASP Markup Formatter Plugin
* Email Extension Plugin
* Mailer Plugin
* SSH Slaves Plugin
* Github Organization Folder Plugin
* Build timeout Plugin
* Git Plugin
* Matrix Authorization Strategy Plugin
* Subversion Plug-in
* Workspace Cleanup Plugin
* CloudBees Folders Plugin
* Gradle Plugin
* PAM Authentication Plugin
* Timestamper

## Declarative Pipelines

```groovy
pipeline {
    agent any
    stages {
        agent {
            label "master"
        }
        options { # Runs before agent provisioning
            timeout(time: 1, unit: 'HOURS')
        }
        steps {
            sh "./might-hang.sh"
        }
    }
    post {
       always {
           echo 'This will always run'
       }
       success {
           echo 'This will run only if successful'
       }
       failure {
           echo 'This will run only if failed'
       }
       unstable {
           echo 'This will run only if the run was marked as unstable'
       }
       changed {
           echo 'This will run only if the state of the pipeline has changed'
           echo 'For example, if the pipeline was previously failing but is now successful'
       }
   }
}
```

### Post Conditions

* cleanup
    * Always run regardless of build or stage result, but runs *after* all other post conditions
* fixed
    * Runs if the current build's status is SUCCESS and the previous build's status was either FAILURE or UNSTABLE
* regression
    * Runs if the current build's status ir woese than the previous build's status

### Tag

* When building a tag you might for example want to run more tests and publish the binaries etc
    * Will run the stage if the tab being built is starting with "release-"
 
```groovy
when {
    tag "release-*"
}
```
 
* To match on any tag there is an alias that doesn't take an argument

```groovy
when {
    buildingTag()
}
```

### Change Request

* Runs the stage if a change Request, a.k.a Pull Request on Github, is currently building

```groovy
when {
    changeRequest()
}
```

* You might for example only care about running static analysis on change requests

```groovy
when {
    not {
        changeRequest()
    }
}
```

* You can also check the various attributes of the pull request for more fine control
    * id, target, branch, fork, url, title, author, authorDisplayName, authorEmail
```groovy
when {
    changeRequest authorEmail: 'rsandel@cloudbees.com'
}
```

### Comparator

* Both changeRequest and tag has gotten on optional parameter; **comparator** where you can set how to compare the values

    * EQUALS - simple string comparison
    * GLOB   - ANT style glob
    * REGEXP - Regular Expression
    
### PreserveStashes

```groovy
options {
    preserveStashes (buildCount: 5)
}
```

## Github

### Setting Up GitHub Webhooks

* Create an access token in GitHub that has permission to read and create webhooks
* Add a GitHub server in Jenkins for Github.com
* Create a jenkins credential with the token and configure the GitHub server configuration to use it
* Check "Manage Hooks" for the GitHub server configuration
* In the project configuration, under "Build Triggers", select "Github hook trigger for GitScm polling"

## Tips

* Try Avoid script
    * Try avoid variables across stages
* When { equals expected: ..., actual: ... }
    * Covers a lot of common usages of when { expression {...} } }
    * No more if (currentBuild.result == "SUCCESS") - just use equals expected: "SUCCEESS", actual: currentBuild.result
    
    
## knowledge Center

### Tomcat

* The username you provided is not allowed to use the text-based Tomcat Manager (error 403) when deploying on remote Tomcat8 using Jenkins
    * Cause : By default Tomcat does not allow access to the manager from external machines
    * Solve: [Link](https://stackoverflow.com/a/41691151)

### Common Errors

#### Send Email With Gmail

[StackOverfow](https://stackoverflow.com/questions/25718290/jenkins-smtp-email-configuration-fails-verification)

Look at this [response](https://stackoverflow.com/questions/25718290/jenkins-smtp-email-configuration-fails-verification/59279178#59279178)

