# cloud-resume-challenge - Front-end CI/CD Pipeline
Tools used
- Github to host my repositry
- Github actions as the ci/cd pipeline
- AWS S3 to host my static website

## What is a CI/CD pipeline
A CI/CD pipeline is a series of automated steps that help software developers/teams deliver code faster, safer and more reliably. [Source](https://circleci.com/blog/what-is-a-ci-cd-pipeline/)

I have created a pipeline that automatically uploads files to my S3 bucket whenever I make a change to the source code and push the code to my Github repository. Below is a visual representation of the process.
![pipeline visualization]()

### Steps on creating a pipeline
The pipeline file is a `.yml` file

The pipeline is made up of:
- Version control system
This is where the code is managed and stored. I used `git` as the VCS and `Github` as the hosting platfrom for my repository

- Continuous integration (CI)


Here's the code block
```
name: S3 Deployment Workflow

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
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
