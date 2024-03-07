# CI/CD AWS Lambda Function with Github Actions



# Using Gitlab instead of Github Actions:
## Save the code below to a new file named .gitlab-ci.yml in your Gitlab project
- Create the CICD variables for AWS access key Id and secret key before saving the .gitlab-ci.yml file.
- This will replace the content from the file lambda_function.py into your target Lambda function: cicd_gitlab_demo_lambda

stages:
  - archiving
  - deploy

archive:
  stage: archiving
  only:
      - main
  image: ubuntu
  when: on_success
  before_script:
      - apt-get update
      - apt-get install zip -y
  script:
      - zip -g $CI_PROJECT_NAME.zip lambda_function.py
  artifacts:
      paths:
      - $CI_PROJECT_NAME.zip


deploy:
  stage: deploy
  image:
    name: amazon/aws-cli
    entrypoint: [""]
  script:
      - aws --version
      - aws configure set region us-east-1
      - aws lambda update-function-code --function-name cicd_gitlab_demo_lambda --zip-file fileb://$CI_PROJECT_NAME.zip
