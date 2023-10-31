# ${projectName}-${teamName}-${artifactId}

Instructions for ${projectName}-${teamName}-${artifactId}


[TOC levels=1-3]: # "# Table of Contents"

# Table of Contents
- [Description](#Description)
- [Prerequisites](#prerequisites)
- [External configurations](#external-configurations)
- [Usage Instructions](#Usage Instructions)
- [Lifecycle for Sharedflow deployment](#Lifecycle for sharedflow deployment) 
    - [Bundle Package](#Bundle Package)
    - [Deploy-Sharedflow](#Deploy Sharedflow)
    - [Upload to Artifactory](#Upload to Artifactory)


## Description
Developer should be able to execute steps manually, in order to deploy sharedflow to Apigee Edge.


## Prerequisites

- JDK 8
- Apache Maven
- Linux/WSL
- Settings.xml file in local 
- SAML Tokens (Access & Refresh)
    

## External configuration

The external configuration references required for token generation and an external settings.xml

### Access token generation

A document for the generating the token is given here:

https://bitbucket.org/hdfc/rbac-scripts/src/master/README.md

Go [here](https://ferguson.login.apigee.com/passcode) to generate temporary auth codes.

### Acurl and get_token


Refer the below readme.md to see the steps to generate the `get_token`

https://bitbucket.org/hdfc/hello-world-api/src/master/README.md

Additional reference doc link: 

https://docs.apigee.com/api-platform/system-administration/auth-tools


### Maven cicd settings xml config

A template for the cicd settings xml (which must be configured into Jenkins as a global config file)
is posted here:

https://bitbucket.org/hdfc/global-maven-settings/src/master/


## Usage Instructions 

This Sharedflow project will be used to deploy hello-world-sf to apigee edge.

How to clone the hello-world-sf and change the directory 

```bash
# clone hello-world-sf repository
git clone https://bitbucket.org/hdfc/hello-world-sf.git


# change directory into the repository
cd hello-world-sf
```


## Lifecycle for Sharedflow Deployment

To make local invocation easier, load the app token from an environment variable

Example of how to export a local environment variable:
```bash
export APIGEE_APITOKEN=abc123
```

### Bundle Package

run this from `edge` directory:

```bash
mvn package \
    -Papigee \
    -Denv=dev \
    -Dorg=ferguson-api \
    -DvhostProtocol=https \
    -DvhostDomainName=devapi2.ferguson.com \
    -DvhostDomainPort=443 \
    -DvhostEdgeName=fei-secure 
```
 
### Deploy Sharedflow

```bash
mvn apigee-enterprise:deploy \
    -Papigee -Denv=dev \
    -Dorg=ferguson-api \
    -DvhostProtocol=https \
    -DvhostDomainName=devapi2.ferguson.com \
    -DvhostDomainPort=443 \
    -DvhostEdgeName=fei-secure \
    -Dapigee.api.port=8080 \
    -Dapigee.api.host=api.enterprise.apigee.com \
    -Dapigee.api.protocol=https \
    -Dbearer=`get_token` \
    -Drefresh=`get_token` \
    -Dtokenurl=https://ferguson.login.apigee.com/oauth/token \
    -Dauthtype=oauth 
```
### Upload to Artifactory 

```bash
Disable for developers. Only CI Server can deploy to the remote location. 
```