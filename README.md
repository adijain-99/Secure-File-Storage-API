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


