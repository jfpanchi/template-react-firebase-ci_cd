# Deploying to Firebase Hosting

This project is set up for automatic deployment to Firebase Hosting using GitHub Actions. Below are the steps necessary to configure the project and perform the deployment.

## Prerequisites

- A Google account.
- A project created in the [Firebase Console](https://console.firebase.google.com/).
- Node.js and `pnpm` installed on your local machine.

## Project Setup

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/your-repository.git
   cd your-repository
   ```
2. **Install Dependencies**:
   ```bash
   pnpm install
   ```
3. **Build the Project**:
   ```bash
   pnpm run build
   ```

## Firebase Configuration
### Create a Service Account
1. Go to the Firebase Console.
2. Select your project.
3. Navigate to Project Settings > Service accounts.
4. Click on Generate new private key. This will download a JSON file containing your credentials.
### Structure of the Service Account Secret
The content of the downloaded JSON file should look like this (this is just an example with fake values):
```json
{
"type": "service_account",
"project_id": "template-hosting-client",
"private_key_id": "some_private_key_id",
"private_key": "-----BEGIN PRIVATE KEY-----\nMIIEvQIBADANBgkqh...<rest of the key>...\n-----END PRIVATE KEY-----\n",
"client_email": "firebase-adminsdk-xxxx@template-hosting-client.iam.gserviceaccount.com",
"client_id": "some_client_id",
"auth_uri": "https://accounts.google.com/o/oauth2/auth",
"token_uri": "https://oauth2.googleapis.com/token",
"auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
"client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/firebase-adminsdk-xxxx%40template-hosting-client.iam.gserviceaccount.com"
}
```
    
### Adding the Secret in GitHub
1. Open the downloaded JSON file in a text editor.
2. Copy the entire content of the file.
3. Go to your repository on GitHub.
4. Navigate to Settings > Secrets and variables > Actions.
5. Click on New repository secret.
6. Name the secret FIREBASE_SERVICE_ACCOUNT and paste the content of the JSON file.
7. Save the secret.

### Configuring ```firebase.json```
Create a file named ```firebase.json``` in the root of your project with the following content:
```
{
  "hosting": {
    "public": "client/dist",  // Change this to the output folder of your build
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ]
  }
}
```

### Configuring GitHub Actions
Create a file ```.github/workflows/deploy.yml``` in the root of your repository with the following content:
```yaml
name: Deploy to Firebase Hosting

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

    - name: Setup Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '18'

    - name: Install pnpm
      run: npm install -g pnpm

    - name: Install dependencies
      run: pnpm install
      working-directory: client

    - name: Build project
      run: pnpm run build
      working-directory: client

    - name: Install firebase-tools
      run: npm install -g firebase-tools

    - name: Deploy to Firebase
      uses: FirebaseExtended/action-hosting-deploy@v0
      with:
        repoToken: '${{ secrets.GITHUB_TOKEN }}'
        firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT }}'
        projectId: 'template-hosting-client'  // Replace with your projectId
        channelId: 'live'

```

### Deployment
When you push to the ```main``` branch, GitHub Actions will execute the workflow and automatically deploy your application to Firebase Hosting.

## Demo deployment

You can view a live demo of the application React at the following link: [Demo Link](https://template-hosting-client.web.app/)