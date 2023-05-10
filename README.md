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
| Users     | us-east-1_AcsQ2NJvp |

| App name | AppClient ID               |
| -------- | -------------------------- |
| User     | 1mn8agsd9pdgr27t5damb62bhr |

4. **Update website config**
   
   Modify the `/js/config.js` file with the setting from user pool and app you created in the previous steps and *upload the file back to your bucket*.
   
   > ```javascript
   > window._config = {
   >     cognito: {
   >         userPoolId: 'us-east-1_AcsQ2NJvp',
   >         userPoolClientId: '3k79leh7v8ouv4ga9l5prtqf1r',
   >         region: 'us-east-1'
   >     },
   >     api: {
   >         invokeUrl: '' // e.g. https://rc7nyt4tql.execute-api.us-east-1.amazonaws.com/prod',
   >     }
   > };
   > ```

5. **Push changes into your Git repo**

6. **Validate your implementation**, [Module 2: User Management](https://aws.amazon.com/getting-started/hands-on/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/module-2/)

---

## Serverless service backend

Use *AWS Lambda* and *DynamoDB* to build a backend process for handling requests for your web application.

1. **Create a DynamoDB table**

2. **Setup table name and partition key**

3. **Get the ARN**, from *Overview* - *Additional info*
   
   `arn:aws:dynamodb:us-east-1:121119844086:table/Users`

4. **Create an IAM role for the Lambda function**
   
   * go to *IAM service*
   
   * create a *service* role with *Lambda use case* (under "Trusted entity type)
   
   * check the box for the role *AWSLambdaBasiceExecutionRole*

5. **Setup role name**

6. **Add the *Create inline policy* permission for DynamoDB**

7. **Operation -> PutItem for ARN of the created table**

8. **Create a Lambda function**
   
   * default Author from scratch
   
   * using [RequestUnicorns](https://webapp.serverlessworkshops.io/serverlessbackend/lambda/requestUnicorn.js) as name for *Node.js 16.x*
     
     * **<u>modify the js file!</u>**
   
   * select the existing role
   
   * replace the existing code from the *"RequestUnicorns"*

9. **Configure test event**
   
   * create a new event
   
   * > ```json
     > {
     >     "path": "/user",
     >     "httpMethod": "POST",
     >     "headers": {
     >         "Accept": "*/*",
     >         "Authorization": "eyJraWQiOiJLTzRVMWZs",
     >         "content-type": "application/json; charset=UTF-8"
     >     },
     >     "queryStringParameters": null,
     >     "pathParameters": null,
     >     "requestContext": {
     >         "authorizer": {
     >             "claims": {
     >                 "cognito:username": "the_username"
     >             }
     >         }
     >     },
     >     "body": "{\"PickupLocation\":{\"Latitude\":47.6174755835663,\"Longitude\":-122.28837066650185}}"
     > }
     > ```

10. **Check if StatusCode==<u>201</u>**

---

## Deploy a RESTful API

Use *Amazon API Gateway* to expose the Lambda function you built in the previous module.

1. **Create REST API using API Gateway service**

2. **Choose Edge optimized**

3. **Create a new resource**
   
   * Set the *resource path* == path described in the *test event* above
   
   * Enable the API Gateway CORS

4. **Create a new method**
   
   * Set the *method* == httpmethod described in the *test event* above
   
   * Select *Lambda function* and select *Use Lambda proxy integration*
   
   * Use the created Lambda function

5. **Setup a new authorization**
   
   * From *method request* modify the *Authorization* field and select **cognito user pool created** above

6. **Deploy your API using Deployment stage == prod**

7. **Update the** `js/config.js` **file**
   
   * setup invokeUrl == `https://idog8vpc82.execute-api.us-east-1.amazonaws.com/prod`

---

---

## TERMINATE TEST TUTORIAL

### Remember to terminate all the resources used and created in this tutorial

- **AWS amplify** - delete app

- **Cognito** - delete pool

- **Lambda function** - delete function and delete pool

- **IAM Role** - delete role

- **DynamoDB Table** - delete table and delete all CloudWatch alarms

- **API Gateway** - delete API

- **CloudWatch Log** - delete log group

---

Author: [Riccardo Ruberto](https://github.com/RiccardoRobb)
