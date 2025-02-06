# Secure-File-Storage-API

## Project: A client runs a SaaS platform where users need to upload, store, and retrieve files securely. They want an API-driven solution that allows users to:

- Upload files to S3 via a pre-signed URL.
- Access/download only their own files securely.
- Ensure no direct public access to S3 objects.
- Log every file upload and access action.

## Solution: 

- API Gateway: Exposes REST endpoints (/upload, /download).
- AWS Lambda: authenticates and generates pre-signed URLs.
- Amazon S3: Stores files securely with bucket policies ofc.
- Amazon DynamoDB : Tracks uploaded files with metadata (user, timestamp).
- AWS CloudWatch: Logs all API calls for auditing


1) Create an S3 Bucket -add 
- Go to AWS S3 → Create bucket.
- Enable public access block (if required).
- Note the bucket name.

![S3 CORS POlicy](https://github.com/user-attachments/assets/9733f95f-889c-41ee-90d7-ab64b9264929)



2) Create an IAM Role for Lambda
- Go to IAM → Roles → Create role.
- Choose AWS Service → Lambda.
- Attach policies:
- AmazonS3FullAccess
- AWSLambdaBasicExecutionRole
- Name the role LambdaS3UploaderRole and create it.

3) Create a Lambda Function
- Go to AWS Lambda → Create function.
- Select Author from scratch.
- Choose:
- Runtime: Node.js 18.x
- Execution Role: Choose LambdaS3UploaderRole
- Click Create function.

4) Write the lambda function

5) Create an API Gateway
- Go to API Gateway → Create API.
- Choose REST API → New API.
- Create a resource /getSignedUrl.
- Create a method → Choose POST.
- Integration Type: Lambda Function.
- Enter your Lambda function name and Deploy API.
- Copy the Invoke URL from API Gateway.

6) Write your react code for app.

- npx create-react-app secure-upload --use-npm //creates "secure-upload" folder in the dir.
- cd secure-upload
- npm install @testing-library/react@latest @testing-library/jest-dom@latest @testing-library/user-event@latest web-vitals@latest
- npm install axios
- change the 'app.js' according to you.

7) npm start
