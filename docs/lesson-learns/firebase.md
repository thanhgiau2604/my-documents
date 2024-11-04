# FIREBASE SERVICES FOR FRONTEND

Before using the services of Firebase, we must create a Firebase project. It's so simple, so we can easily go to the Firebase console and create a project.

## 1. **Frontend Setup**

Install package:
```bash
yarn add firebase
```

- In your firebase project, click on the Web icon (`</>`) to add a web app
- Follow the prompts to register your app, then add the config object to firebase config file.

Full config file:

```ts
import { getAnalytics } from 'firebase/analytics'
import { getApps, initializeApp } from 'firebase/app'
import { getAuth, GoogleAuthProvider, signInWithPopup } from 'firebase/auth'
import { collection, doc, getFirestore } from 'firebase/firestore'
import { getFunctions } from 'firebase/functions'
import { getStorage } from 'firebase/storage'
import { clientConfig } from '@/configs'

const app =
  getApps().length === 0
    ? initializeApp({ ...clientConfig, storageBucket: process.env.NEXT_PUBLIC_STORAGE_BUCKET })
    : getApps()[0]

// Google Login setup
const provider = new GoogleAuthProvider()

provider.setCustomParameters({
  prompt: 'select_account',
})

const auth = getAuth(app)
const db = getFirestore(app)
const storage = getStorage(app)
const functions = getFunctions(app, 'asia-northeast1')
const analytics = typeof window !== 'undefined' ? getAnalytics() : null
export { db, auth, storage, functions, analytics }
```

## 2. **Firebase Authentication**
- Go to Firebase console → follow the instruction on console to enable the feature
- Authorized domains: At Settings tab → select menu `Authorized domains` → Add Domain being used
- In firebase config file, add this:
 ```ts
export const signInWithGooglePopup = () => signInWithPopup(auth, provider)
```
Note: After login success, Google return only the `user` logged in including credential such as token, user info,.. not manage session for my app. To achive this, we must integrate with other external package as  `next-firebase-auth-edge`.

Why using **next-firebase-auth-edge**: 
- Aim to solve the problem of creating and verifying custom JWT tokens provided by Firebase Authentication using Web Crypto API available inside Edge runtimes.
- It allows for server-side rendering, ensuring that authentication checks are performed before rendering pages, which enhances security and user experience.

Official doc: https://next-firebase-auth-edge-docs.vercel.app/docs/getting-started

Tutorial: https://hackernoon.com/using-firebase-authentication-with-the-latest-nextjs-features

## 3. **Firebase Storage**
Before uploading a file to Firebase Storage on Client side, ensure that you have already defied `storage rules` allowing to upload to specific path.

Example function `UploadFileToStorage` by specific path:
```ts
export const uploadFileToStorage = async (
  file: File,
  path: string,
  customMetadata?: { [key: string]: string }
): Promise<UploadFileResult> => {
  let result = null
  let error = ''

  try {
    const storageRef = ref(storage, path)
    await uploadBytes(storageRef, file, {
      customMetadata,
      contentDisposition: 'attachment',
    })
    result = await getDownloadURL(ref(storage, path))
  } catch (e) {
    error = (e as Error).message
  }

  return { result, error }
}
```
This function will return the download url of the uploaded file, and error info if an error occur.

Related Notes: using `firebase deploy --only storage` to deploy storage rules.

## 3. **Firestore Database**
Same as Storage, we need to define the rules before interacting with Firestore Database. If not, an error `Missing or insufficient permissions` will be thrown on browser console.

Example firestore rules:

```ts
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    function isLogined() {
      return request.auth != null
    }

    function authUid() {
      return request.auth.uid
    }

    function isAuthorized(uid) {
      return isLogined() && authUid() == uid
    }

    match /users/{userId} {
      allow read: if isLogined()
      allow update: if isAuthorized(userId)
    }
  }
}
```

The helpful functions we can use:
- getDoc / getDocs
- setDoc
- updateDoc
- deleteDoc
- writeBatch

Related Notes: using `firebase deploy --only firestore:rules` to deploy firestore rules.

## 4. **Firebase functions**
How to call a firebase function?
Just define like this:
```ts
import { httpsCallable } from 'firebase/functions'
import { functions } from '@/utils/firebase' // exported from config file

export const uploadFile = httpsCallable<UploadFilePayload, FunctionResponse<string>>(
  functions,
  'onCallUploadFile',
  {
    timeout: REQUEST_TIMEOUT.file_process,
  }
)
```
Using:
```ts
try {
  const { data } = await uploadFile(payload)
} 
catch (error) {
  // handle error
}
```

Related notes: using `firebase deploy --only functions:<function_name>` to deploy a specific function.

## 5. **Firebase Analytics**
- To use Analytics feature, we need to enable this service.
- After enabling, the config object will be added `measurementId` property. So that, we must update config object in .env file
- We can add some log event for tracking, example:
```ts
import { logEvent } from 'firebase/analytics'
import { analytics } from './firebase'

// when user search by a keyword, log this event to Analytic Events.
export const logSearchEvent = (params: { searchValue: string }) => {
  if (analytics) {
    logEvent(analytics, 'search', params)
  } else {
    console.error('analytics is not setted.')
  }
}
```

**View logged events**: on Firebase console, expand tab `Analytics` and select `Events`.

