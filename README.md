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


### 1) Create an S3 Bucket -add 
- Go to AWS S3 → Create bucket.
- Enable public access block.
- Note the bucket name.

![S3 CORS POlicy](https://github.com/user-attachments/assets/9733f95f-889c-41ee-90d7-ab64b9264929)



### 2) Create an IAM Role for Lambda
- Go to IAM → Roles → Create role.
- Choose AWS Service → Lambda.
- Attach policies:
- AmazonS3FullAccess
- AWSLambdaBasicExecutionRole
- Name the role LambdaS3UploaderRole and create it.

![IAM Role policies](https://github.com/user-attachments/assets/5e864105-c246-4423-a167-5a223ad18a1d)


### 3) Create a Lambda Function
- Go to AWS Lambda → Create function.
- Select Author from scratch.
- Choose:
- Runtime: Node.js 18.x
- Choose execution Role: LambdaS3UploaderRole
- Click Create function.

````
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import { getSignedUrl } from "@aws-sdk/s3-request-presigner";

// Initialize S3 client with the correct region
const s3 = new S3Client({ region: "eu-west-2" }); // Europe (London)
const BUCKET_NAME = "your-bucket-name";

export const handler = async (event) => {
    try {
        // Log the incoming event (for debugging)
        console.log("Received event:", JSON.stringify(event));

        // Check if the body is missing
        if (!event.body) {
            throw new Error("No body in the request");
        }

        // Parse the body and extract fileName and fileType
        const { fileName, fileType } = JSON.parse(event.body);

        // Log the parsed values (for debugging)
        console.log("Parsed values:", fileName, fileType);

        const s3Params = {
            Bucket: BUCKET_NAME,
            Key: `uploads/${fileName}`,
            ContentType: fileType,
        };

        // Generate a pre-signed URL using AWS SDK v3
        const command = new PutObjectCommand(s3Params);
        const uploadURL = await getSignedUrl(s3, command, { expiresIn: 60 });

        // Return the URL to the client
        return {
            statusCode: 200,
            body: JSON.stringify({ uploadURL }),
            headers: {
                "Content-Type": "application/json",
                "Access-Control-Allow-Origin": "*", // CORS
            },
        };
    } catch (error) {
        console.error("Error:", error.message);

        return {
            statusCode: 500,
            body: JSON.stringify({ error: error.message }),
            headers: {
                "Access-Control-Allow-Origin": "*", // CORS
            },
        };
    }
};

````
- Test the function
- Deploy the function

![Lambda](https://github.com/user-attachments/assets/907eee0d-375c-44f5-9d40-2827df1dbe3d)


### 4) Create an API Gateway
- Go to API Gateway → Create API.
- Choose REST API → New API.
- Create a resource /upload.
- Create a method → Choose POST.
- Integration Type: Lambda Function.
- Enter your Lambda function name and Deploy API.
- Copy the Invoke URL from API Gateway.

![API gateway](https://github.com/user-attachments/assets/dac08444-a319-4b9c-ab0d-7821bb11f4c0)

- Create OPTIONS method with Mock integration
- Add method response and response body as shown in the figure below: 

![method reponse options](https://github.com/user-attachments/assets/8550ca08-ca6f-4c01-a47d-5fd53f50c041)

- Add integration response and mapping templates as shown below: 

![integration response options](https://github.com/user-attachments/assets/ba5fcf08-5f01-4ba1-adb2-0c90e25c3371)

- We will configure POST method now
- Add method response and response body as shown in the figure below: 

![mr post](https://github.com/user-attachments/assets/cb36082d-d8da-4309-a452-255dc20257fc)


- Add integration response and mapping templates as shown below:
- Take care that the integration response is proxy integration - that means you have to add CORS headers in your lambda function

![ir post](https://github.com/user-attachments/assets/38e62b2e-58f6-4b5c-b616-ac8965c6833c)

### 5) Enable CORS in your resource

- Go to your 'resources' in your API gateway and then click 'Enable CORS' button in top right corner.
- Select everything as shown below: 

![Enable CORS](https://github.com/user-attachments/assets/5d161ff9-a74b-46c5-9706-e016a6f837d6)

- Deploy your API now in the new stage named 'dev' or 'prod'.


### 5) Write your react code for app.

- npx create-react-app secure-upload --use-npm //creates "secure-upload" folder in the dir.
- cd secure-upload
- npm install @testing-library/react@latest @testing-library/jest-dom@latest @testing-library/user-event@latest web-vitals@latest
- npm install axios
- change the 'app.js' according to you.

### 7) npm start

![Upload success](https://github.com/user-attachments/assets/d6c11950-58db-4396-9519-89bf6bad8aa9)

