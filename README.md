# Serverless web application

Refer to [Build a Serverless Web Application with AWS Lambda, Amazon API Gateway, AWS Amplify, Amazon DynamoDB, and Amazon Cognito](https://aws.amazon.com/getting-started/hands-on/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/?nc1=h_ls)

<img src="./images/Architecture.png" title="" alt="Architecture" data-align="center">

## Static web Hosting with Continuous deployment

All of your static web content will be managed by *AWS Amplify Console*. Your end users will then access your site using the <u>public website URL exposed by AWS Amplify</u> Console, no web server or other services are required.

1. **Select a Region**, will be used for all the services used in this application

2. **Create a Git repository**, in *AWS CodeCommit console*
   
   * set the repo name
   
   * set up an <u>*IAM user* with GIt credentials</u>
   
   * select clone HTTPS

3. **Populate the Git repository**, copy the website content from an existing publicly accessible *S3 Bucket*
   
   * change directory into your repo and copy the static files from S3
     
     `cd <web-site-name>`
     
     `aws s3 cp s3://<aws-name>-<region>/WebApplication/1_StaticWebHosting/ website ./ --recursive`
   
   * commit the files to your Git service

4. **Enable web hosting with AWS Amplify Console**
   
   * create a *New App* and choose *Host web app*
   
   * select the repository service provide and commit code

5. **Setup AWS Amplify to automatically deploy all files hosted**

---

## User management

You'll deploy pages that enable customers to register as a new user, verify their email address and sign into the site, using *Amazon Cognito*.

1. **Create Cognito user pool**, configure *sign-in experience*

2. **Configure security requirements / sign-up experience / message delivery**

3. **Take note of PoolID, AppClient ID and Pool details**

| Pool name | PoolID              |
|:---------:|:-------------------:|
| Users     | us-east-1_8eFiOt3Is |

| App name   | AppClient ID               |
| ---------- | -------------------------- |
| magic_note | 24s1cskeupgk8af9ak5nn71vjj |

4. **Update website config**
   
   Modify the `/js/config.js` file with the setting from user pool and app you created in the previous steps and *upload the file back to your bucket*.
   
   `window._config = {
       cognito: {
           userPoolId: 'us-east-1_8eFiOt3Is',
           userPoolClientId: '24s1cskeupgk8af9ak5nn71vjj',
           region: 'us-east-1'
       },
       api: {
           invokeUrl: '' // e.g. https://rc7nyt4tql.execute-api.us-east-1.amazonaws.com/prod',
       }
   };`
   
   
