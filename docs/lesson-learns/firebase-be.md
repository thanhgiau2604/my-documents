# FIREBASE SERVICES BACKEND

**Backend setup**

Install firebase tool
```bash
npm install -g firebase-tools
```
Login firebase
```bash
firebase login
```

Setup firebase admin
1. Generate a Service Account Key

2. Install package
```bash
yarn add firebase-admin
```
3. Create config file
Example:
```ts
import * as admin from "firebase-admin";

// Option 1. using Default Credential associated with the Google Cloud project
if (admin.apps.length < 1) {
  admin.initializeApp({
    credential: admin.credential.applicationDefault(),
    storageBucket: "<bucket>"
  });
}
// Option 2. using Credential created at step 1
const serviceAccount = JSON.parse(process.env.FIREBASE_ADMIN_SDK_JSON); 
if (admin.apps.length < 1) {
  admin.initializeApp({
    credential: admin.credential.cert(serviceAccount),
    storageBucket: "<bucket>"
  });
}

const db = admin.firestore();
const auth = admin.auth();
const storage = admin.storage();
const bucket = storage.bucket();

const FieldValue = admin.firestore.FieldValue;
const FieldPath = admin.firestore.FieldPath;
const Timestamp = admin.firestore.Timestamp;
const GeoPoint = admin.firestore.GeoPoint;

db.settings({ ignoreUndefinedProperties: true });

export { db, auth, storage, bucket, FieldValue, FieldPath, GeoPoint, Timestamp };
```

3. Create env file
.env.default
```bash
FIREBASE_ADMIN_SDK_JSON='{"type": "service_account", "project_id": "YOUR_PROJECT_ID", "private_key_id": "YOUR_PRIVATE_KEY_ID", "private_key": "-----BEGIN PRIVATE KEY-----\nYOUR_PRIVATE_KEY\n-----END PRIVATE KEY-----\n", "client_email": "YOUR_CLIENT_EMAIL", "client_id": "YOUR_CLIENT_ID", "auth_uri": "https://accounts.google.com/o/oauth2/auth", "token_uri": "https://oauth2.googleapis.com/token", "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs", "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/YOUR_CLIENT_EMAIL"}'
FIREBASE_STORAGE_BUCKET=<bucket>
```