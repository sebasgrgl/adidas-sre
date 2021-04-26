# adidas-sre

adidas-sre is a demo project which aims to show a complete CI/CD flow pipeline for a simple Java SpringBoot application

## Tools Used

- GitHub as source Code Repository
- Jenkins as CI/CD tool to build the pipeline
- Google Cloud Compute Engine to deploy the Jenkins VM
- Java as coding language (using a prebuild code from https://spring.io/quickstart)
- Maven as Compiler
- Docker as Container Builder
- Google Cloud Registry as image repository
- Google Cloud Kubernetes Engine as Kubernetes infrastructure provider

## Diagram of the solution

## Jenkins and Pipeline

Deployed in Google Cloud VM provides the infrastructure to build the pipeline
The pipeline reads the three branches and builds automatically checking every 5 minutes for changes over the code (Can be implemented as a webhook too) and has the following stages:

- Checkout SCM
- Tool install
- Set Environment variables
- Build
- Test
- Build Docker Image
- K8s Deploy

## Repository

Using common branching strategy, the repository contains three main branches: production, staging and development which have their corresponding infrastructure deployed on Google Cloud Kubernetes Engine cluster.

## Usage

Because this is a CI/CD pipeline, every change on a branch will launch on Jenkins and be deployed on K8S. This can be improved adding some Checkpoints and validations for example, an authorization for deploying on production environment.
