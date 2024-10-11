# cloud-resume-challenge - Front-end CI/CD Pipeline
Tools used
- Github to host my repositry
- Github actions as the ci/cd pipeline
- AWS S3 to host my static website

## What is a CI/CD pipeline
A CI/CD pipeline is a series of automated steps that help software developers/teams deliver code faster, safer and more reliably. [Source](https://circleci.com/blog/what-is-a-ci-cd-pipeline/)

I have created a pipeline that automatically uploads files to my S3 bucket whenever I make a change to the source code and push the code to my Github repository. Below is a visual representation of the process.
![pipeline visualization](https://github.com/achenchi7/cloud-resume-challenge/blob/main/images/cicd2.png)

### The workflow
The pipeline file is a `.yml` file in the `.github/workflows` folder that defines the steps to be followed to update the s3 bucket if any changes in the code have been committed.


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
    - name: Checkout repository # Step no.1 is to check the repo. This is the name of the step. You can give it any name you want
      uses: actions/checkout@v2 # The repo is checked out using this action

    - name: Configure AWS credentials # Step no.2 is to configure access to your AWS.
      uses: aws-actions/configure-aws-credentials@v2 # The step is carried out using this action
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1  # Make sure this matches your S3 bucket region

    - name: Sync files to S3 # Step no.3 in the deploy job is to sync the files in the repo to my s3 bucket in AWS
      run: |
        aws s3 sync . s3://${{ secrets.S3_BUCKET_NAME }} --exclude ".git/*" --delete --content-type text/html
```
### Github secrets configuration
The workflow file will need access to the s3 bucket in my AWS account. As security best practice to grant access to AWS, I created an IAM user and granted the user read and write access to my s3 bucket. I then generated `ACCESS_KEY` and `SECRET_ACCESS_KEY` to grant programmatic access to the AWS account.

As security best practice, I stored the `ACCESS_KEY` and `SECRET_ACCESS_KEY` as secrets in my repository through the following procedure:
- Go to repository settings
- Navigate to **Secrets and variables > Actions**
- Add `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` as secrets.

### Trigger the workflow
After the workflow file is set up, it will run automatically based on when the workflow is triggered every time there’s a `push` to the main branch.

`Push Changes`: To push changes to trigger the workflow:
```git
git status
git add .
git commit -m "commit message"
git push -u origin main
```
Here's a screenshot of a successfully deployed workflow
![Deploy successful](https://github.com/achenchi7/cloud-resume-challenge/blob/main/images/Screenshot%20(205).png))
