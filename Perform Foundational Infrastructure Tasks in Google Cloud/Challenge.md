## Solving tasks

### Task 1: Create a bucket

1. Navigation menu > **STORAGE** > Storage > Create Bucket
2. Name your bucket > Enter **GCP Project ID** > Continue
3. Choose where to store your data > **Region:** us-east1 > Continue
4. Use default for the remaining 
5. Create

### Task 2: Create a Pub/Sub topic

1. Navigation menu > **BIG DATA** > Pub/Sub
2. Create Topic > **Name:** Jooli > Create Topic

### Task 3: Create the thumbnail Cloud Function

1. Navigation menu > **COMPUTE** >  Cloud Functions > Create Function

2. Use the following config:

   **Name:** CFJooli
   **Region:** us-east1
   **Trigger:** Cloud Storage
   **Event type:** Finalize/Create
   **Bucket:** BROWSE > Select the qwiklabs bucket

3. Remaining default > Next

4. **Runtime:** Node.js 10
   **Entry point:** thumbnail
5. Add the code appropiately
6. Download the image from URL
7. Navigation menu > **STORAGE** > Storage > Select your bucket > Upload files
8. Refresh bucket

### Task 4: Remove the previous cloud engineer

1. Navigation menu > IAM & Admin
2. Search for the "**Username 2**" > Edit > Delete Role
