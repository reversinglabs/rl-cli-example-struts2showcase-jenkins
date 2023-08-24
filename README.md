# ReversingLabs rl-secure Jenkins Examples

This repository contains working examples of Jenkins pipeline scripts to illustrate scanning with the [ReversingLabs secure.software CLI](https://docs.secure.software/cli/).

ReversingLabs secure.software CLI is capable of scanning [nearly any type](https://docs.secure.software/concepts/language-coverage) of software artifact or package that results from a build.

In these examples, we're using the source code and Maven build instructions for the Struts2 showcase web app, which came with [Apache Struts v2.5.28](https://archive.apache.org/dist/struts/2.5.28/).

The following examples are provided in this repository:

- **Jenkinsfile_docker**
- **Jenkinsfile**

Make sure you have the [HTML Publisher plugin for Jenkins](https://plugins.jenkins.io/htmlpublisher/) installed.

Both scripts display the ReversingLabs HTML report on the Jenkins UI.
The report will not be displayed correctly unless you change the CSP header as explained in [the official Jenkins documentation](http://www.jenkins.io/doc/book/security/configuring-content-security-policy/#customizing-content-security-policy).

To integrate the ReversingLabs secure.software CLI with Jenkins, follow the step-by-step [Jenkins integration guide](https://docs.secure.software/cli/integrations/jenkins).


## Jenkinsfile_docker

This pipeline script builds the WAR file and scans it using the [ReversingLabs rl-scanner Docker image](https://hub.docker.com/r/reversinglabs/rl-scanner).

Using the Docker image is ideal for ephemeral instances of Jenkins because it does not require the ReversingLabs secure.software CLI to be installed.

After the file is scanned, analysis reports in JSON, CycloneDX, and SPDX formats are published as a build artifact.
The HTML report is published as well. 

The script requires that you create the `RLSECURE_ENCODED_LICENSE` and `RLSECURE_SITE_KEY` secret credentials within Jenkins to store your ReversingLabs [license and site key](https://docs.secure.software/cli/deployment/rl-deploy-quick-start#prepare-the-license-and-site-key).


## Jenkinsfile

This script is useful in cases when the ReversingLabs secure.software CLI is installed on a persistent (non-ephemeral) Jenkins server.

It requires that you install the CLI to `/bin/RLSecure` and initialize the package store at the same location.

After the file is scanned, analysis reports in JSON, CycloneDX, and SPDX formats are published as a build artifact.
The HTML report is published as well. 
