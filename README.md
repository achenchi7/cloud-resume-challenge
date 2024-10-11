# cloud-resume-challenge - Front-end CI/CD Pipeline
Tools used
- Github to host my repositry
- Github actions as the ci/cd pipeline
- AWS S3 to host my static website

## What is a CI/CD pipeline
A CI/CD pipeline is a series of automated steps that help software developers/teams deliver code faster, safer and more reliably. [Source](https://circleci.com/blog/what-is-a-ci-cd-pipeline/)

I have created a pipeline that automatically uploads files to my S3 bucket whenever I make a change to the source code and push the code to my Github repository. Below is a visual representation of the process.
![pipeline visualization](https://github.com/achenchi7/cloud-resume-challenge/blob/main/images/cicd2.png)

### Steps on creating a pipeline
The pipeline file is a `.yml` file that defines the steps to be followed to update the s3 bucket if any changes in the code have been commited.


Here's the code block
```yml
name: S3 Deployment Workflow # This is the name of the workflow 

on:
  push:         # This is the action that triggers the pipeline
    branches: 
      - main    # This is the branch in which changes will be made.

jobs:          # This keyword defines what the pipeline will do 
  deploy:      # Job no.1 is to deploy
    runs-on: ubuntu-latest

    steps:       # Defines the steps to be followed in deployment
    - name: Checkout repository # Step no.1 is to check the repo
      uses: actions/checkout@v2

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1  # Make sure this matches your S3 bucket region

    - name: Sync files to S3
      run: |
        aws s3 sync . s3://${{ secrets.S3_BUCKET_NAME }} --exclude ".git/*" --delete --content-type text/html
```
