# ARWEAVE BLOCKCHAIN INTRODUCTION

## What is Arweave - Ardrive?
1. **Arweave**
- Arweave (AR) is a blockchain protocol that enables permanent data storage with a single, one-time fee. 
- Arweave aims to provide a decentralized and reliable data storage service
2. **Ardrive**
- ArDrive is a decentralized and permanent file storage application built on the Arweave blockchain. It offers users a secure and reliable way to store their files for the long term.
- Key features of ArDrive includes:
  - Permanent storage
  - Security
  - Decentralization
  - No subscription fees
  - User-friendly interface

- In order to use this service, you'll need an Arweave wallet to interact with the blockchain and store your files. I will tell you in next part.

## Arweave wallet
- The wallet lets you deploy permanent data from the browser, connect your accounts securely to decentralized applications.
- Setting up your wallet is super easy, simply open up https://arweave.app in your browser and then click on the `icon +` on the bottom left of the screen (more on: https://docs.arweave.org/developers/wallets/arweave-wallet)
- Another option, you can open https://app.ardrive.io/, click `Sign up` button and choose `Create a wallet`.
- If you generate with https://arweave.app site, you need to import it to Ardrive app to create an Ardrive account associated with Arweave wallet.

## What we used in the Kanban Noary Drive project
**Requirement**: 
- In this services, user can upload files and it will be stored on a dispersed blockchain storage called Arweave. 
- Once uploaded the files will never be deleted. Users are able to purchase extra storage space easily.
- The upload files must be encrypted before uploading to the blockchain.
- Can upload for free if file size smaller than 100KB.

**With above requirement, our flow as below:**
- if file size < 100KB, we will upload through Ardrive, it's totally free and fully supports encrypting file data before uploading to Arweave blockchain.
- if file size >= 100KB, we will use exported functions from Ardrive to encrypt data, save as buffers, and then upload to Arweave directly.
- To view uploaded files, we will get original decrypted buffers from Arweave and then use function from Ardrive to decrypt the buffers.

**Packages installed:**
```bash
yarn add arweave
yarn add ardrive-core-js
```

Helpful functions and related documents:

- Encrypt buffer:
```js
import { deriveFileKey, fileEncrypt } from "ardrive-core-js";

const fileKey = await deriveFileKey(fileId, driveKey);
const encryptData = await fileEncrypt(fileKey, buffer)
```
- Decrypt buffer:
```js
import { deriveFileKey, fileDecrypt } from "ardrive-core-js";

const fileKey = await deriveFileKey(fileId, driveKey);
const address = await wallet.getAddress()
const decryptData = await fileDecrypt(txCipherIV, fileKey, buffer)
```
- Upload through Ardrive: https://docs.ardrive.io/docs/core-sdk.html
- Upload through Arweave: 
  - Create a transaction with buffer going to upload: 
  ```js
    const transaction = await createTransaction(buffer);
  ```
  - Sign transaction:
  ```js
   await signArweave(transaction)
   ```
  - Upload:
  
  ```js
    const uploader = await arweave.transactions.getUploader(transaction);
    while (!uploader.isComplete) {
      await uploader.uploadChunk();
    }
    // get reward price
    const { ar, winston } = getRewardPrice(transaction);
  ```
