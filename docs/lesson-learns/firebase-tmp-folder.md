## Using `tmp` system folder of firebase function for temporary storage.

### 1. Introduce and common use cases
The `/tmp` folder in Firebase Cloud Functions is a temporary filesystem available during the execution of your function. 
It is a RAM disk, which means it resides in memory.
You might use this folder in specific scenarios where you need temporary storage but don't want to store files permanently.
Note that it will consume memory resources provisioned for the function. So, remember to clean up resources in this folder to avoid memory leakage problems.

Here are common use cases for using the `/tmp` folder in Firebase Cloud Functions:
- **Processing Temporary Files:**
  - Download a file from Cloud Storage.
  - Process the file (e.g., resize using Sharp).
  - Save the processed file temporarily in `/tmp`.
  - Upload the processed file back to Cloud Storage.
- **Caching during Execution:**
  - You can use `/tmp` as a temporary cache. This could be useful for tasks like generating reports, compiling, or other short-lived tasks.
- **Handling Large File Uploads:**
  - For large file uploads or if you need to split large files into smaller chunks, you can write the pieces to `/tmp`, process them, and combine or upload the result to Cloud Storage or another service.
- **Creating Temporary Logs or Debug Files:**
  - You might write log files or debug information to the `/tmp` folder. These logs will be available for the duration of the function execution but won’t persist beyond that.

### 2. Example
- **Goal**: Decrypt all encrypted files and zip all of them into one file and store it in Firebase Storage.
- **Language**: NodeJS

In this example, I called `os.tmpdir()` to get the temporary folder of OS. I have`/tmp` folder for my case. And here is my flow logic:
1. Define destination temporary folder for storing downloaded file (`/tmp/downloads`) and init new zip object.
2. decryptAndWriteFileInFolder: I looped over all files in folders → decrypt my content (only for my logic) → write in the `/tmp/downloads` folder.
3. Create new temporary folder for storing zip file → `/tmp/zip` and use `zip.addLocalFolder` to add target folder for zip.
4.  Use `zip.writeZip(zipFilePath)` to write zip file.
5.  Upload to storage
6.  Clean up

Code:
```ts
import zip from 'adm-zip';

// create downloads folder to store all download file
const downloadDir = `${os.tmpdir()}/downloads`;
if (!fs.existsSync(downloadDir)) {
    fs.mkdirSync(downloadDir);
}

const z = new zip();

await decryptAndWriteFileInFolder(downloadDir,...)

const zipDir = `${os.tmpdir}/zip`;
if (!fs.existsSync(zipDir)) {
    fs.mkdirSync(zipDir);
}

// compress into one zip file
const zipFileName = `${replaceSlash(folderName)}.zip`;
const zipFilePath = path.join(zipDir, zipFileName);
z.addLocalFolder(path.join(downloadDir))
z.writeZip(zipFilePath);

// upload zipped file to storage
const res = await bucket.upload(zipFilePath, {
    destination: `downloads/${folderId}.zip`,
    metadata: {
      contentType: 'application/zip',
      contentDisposition: `attachment; filename=${zipFileName}`
      }
    })

// clean up
fs.rmdirSync(downloadDir, { recursive: true });
fs.rmdirSync(zipDir, { recursive: true });
```

https://cloud.google.com/appengine/docs/standard/using-temp-files?tab=node.js#top

