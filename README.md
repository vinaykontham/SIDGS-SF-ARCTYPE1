# API Archetype


This project, api-archetype, is the project API auto-generator component.
[TOC levels=1-3]: # "# Table of Contents"

# Table of Contents
- [API Archetype](#api-archetype)
- [Prerequisites](#prerequisites)
    - [External configurations](#external-configurations)
- [Installation and Usage Instructions](#installation-and-usage-instructions)
    - [Installation](#installation)
    - [Usage](#usage)
    - [After proxy generation](#after-proxy-generation)
        - [Optional Step (based on requirement): Remove OAuth Validation:](#Optional Step (based on requirement): Remove OAuth Validation )
        - [Create repo in bitbucket:](#create-repo-in-bitbucket)
        - [Initialize repo and branches: master, develop](#initialize-repo-and-branches-master-develop)
        - [Update pom file w/artifactId.](#update-pom-file-wartifactid)
- [See also](#see-also)

Developer should be able to execute steps manually, in order to build the reverse proxy api
archetype.

# Prerequisites

1. Apache Maven
2. Git
3. Linux/WSL

## External configurations

Please the external configuration references required for this project. For example, this project
depends on an external settings.xml that is hosted in jenkins as cicd-settings-file.xml.

A template for the cicd settings xml (which must be installed into Jenkins as a global config file)
is posted here: https://bitbucket.org/hdfc/global-maven-settings/src/master/

TODO: add this as a submodule later

# Installation and Usage Instructions 

## Installation

How to install the api generator to your local environment:
```bash
# clone api-archetype repository
git clone https://bitbucket.org/hdfc/api-archetype.git

# change directory into the repo
cd api-archetype

# install maven dependencies
mvn install
```

## Usage

How to run the api generator

Api archetypes need to be generated outside the repo.

```bash
# create and change to new directory; don't have to use ~/tmp
mkdir ~/tmp
cd ~/tmp

# generate the api-archetype
# TODO: update this example to match account-credit-score from Sprint 0 doc
mvn archetype:generate \
  -DarchetypeGroupId=com.ferguson.apigee.archetype \
  -DarchetypeArtifactId=api-pass-through \
  -DarchetypeVersion=1.0.0-SNAPSHOT \
  -Dpackage=com.ferguson.api \
  -DinteractiveMode=false \
  -DgroupId=com-ferguson-api \
  -DteamName=demo \
  -DartifactId=hello-world-api 
```

Files will be generated for CICD onboarding of reverse proxy api under the
`hello-world-api/($artifactId)` folder.

These files can be checked into the git repository of respective api proxy.

## After proxy generation

From here, the generated proxy needs to be uploaded to git (bitbucket).

Initialize a new git repo according to the following document:
[FEI-Best Practices for Naming Conventions.pdf](https://apigeesc.atlassian.net/wiki/download/attachments/604602514/FEI-Best%20Practices%20for%20Naming%20Conventions.pdf?api=v2).
See _SCM Repo Naming Convention_ on page 6.

### Optional Step (based on requirement): Remove OAuth Validation 

If any service is not supporting OAuth so remove the OAuth velidation before onboarding the generated API to the Bitbucket. 

```
# Change dir 
cd edge/apiproxy/proxies

# Edit default.xml file and look for PreFlow 
<PreFlow name="PreFlow">
        <Request>
            <Step>
                <Name>FC-OauthVerifyToken</Name> 
            </Step>
            <Step>
                <Name>RC-cache</Name>
            </Step>
            <Step>
                <Name>SA-spikeArrest</Name>
            </Step>
            <Step>
                <Condition>(request.verb = "POST")</Condition>
                <Name>TP-jsonContentProtection</Name>
            </Step>
        </Request>
        <Response/>
</PreFlow>

# Remove the flow callout element 
<Step>
    <Name>FC-OauthVerifyToken</Name> 
</Step>

# Save the configurations 
```

```
# Switch the dir 
edge/apiproxy/policies

# Remove the Oauth Validation policy 

FC-OauthVerifyToken.xml
```

```
# Switch the dir and edit the file 
edge/apiproxy/fei-__teamName__-__artifactId__.xml

# Remove  flow callout element from policies attribute 
<Policy>FC-OauthVerifyToken</Policy>

# Save the configurations 
```

### Create repo in bitbucket

Repo naming formats
```
1. centralized model (default): `{group_name}-{team-name}-{api-name}`
2. federated model: `{group_name}/{team-name}-{api-name}`
```

Example: https://{hostname}/hello-world-api.git

### Initialize repo and branches: master, develop

Later, initialize feature, release and hotfix branches as needed

```
# change directory into new repo
cd hello-world-api    

#initialize git
git init
git add .
git commit -m "COMMIIT MESSAGE"

#setup branches
git checkout -b develop

#push repo w/branches to git host
git remote add origin https://{hostname}/hello-world-api.git
git push -u origin master
git push origin --all
```

### Update pom file w/artifactId.

In this case, it would be `hello-world-api`.

```
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com-ferguson-api</groupId>
    <artifactId>hello-world-api</artifactId>
    <version>1</version>
</project>
```

# See also

There is a document for setting up the corresponding jenkins job
[here](https://hdfcclover.atlassian.net/wiki/download/attachments/774275222/FEI-%20API%20Archetype%20Generator%20Guidelines.pdf)
