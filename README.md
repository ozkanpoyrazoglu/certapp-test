# Jenkins Pipeline for Certificate Checker App

Certapp-test repo created for [Certificate Checker](https://github.com/ozkanpoyrazoglu/certificate-debugapp.git) app. A version that can be used with different applications and different repositories will be added in the future.

This repository basically contains two products; Jenkins pipeline and helm template. As you check the pipeline script Helm charts update values.yaml and charts.yaml and pushes into 'develop' branch for next step: ArgoCD.

## Usage

For using this pipeline in Jenkins, you can create a Pipeline, name it; and in Pipeline section you select "Pipeline script from SCM, and select GıT. Than give this repository url and "master" branch.

You can add webhook to trigger this pipeline from Certificate Checker app https://github.com/ozkanpoyrazoglu/certificate-debugapp .
