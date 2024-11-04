---
sidebar_position: 4
---

## STEPS TO DEPLOY REACT APP TO S3, CLOUDFRONT AND AUTOMATE WITH GITHUB ACTION  

Reference: https://antonputra.com/amazon/deploy-react-to-s3-and-cloudfront 
### 1. Create simple react app <a name='reactapp'></a>
- Create project
  ```bash
  npx create-react-app demo-deployment
  ```

- Install react-router-dom v6
    ```bash
    npm install react-router-dom@6 / 
    yarn add react-router-dom@6
    ```

- Create `src/pages/login.js`:
    ```js
    import React from "react";
    import { Link } from "react-router-dom";

    const LoginPage = () => {
    return (
        <div className="app">
        <h1>Login page</h1>
        <Link to="/signup">Go to signup</Link>
        <Link to="/">Back home</Link>
        </div>
    );
    };

    export default LoginPage;
    ```

- Create `src/pages/signup.js`:
  ```js
  import React from "react";
    import { Link } from "react-router-dom";

    const SignupPage = () => {
    return (
        <div className="app">
        <h1>Signup Page</h1>
        <Link to="/login">Go to login</Link>
        <Link to="/">Back home</Link>
        </div>
    );
    };

    export default SignupPage;
    ```

- Create `src/pages/home.js`:
  ```js
    import React from "react";
    import { Link } from "react-router-dom";

    const HomePage = () => {
    return (
        <div className="app">
        <h1>HOME PAGE</h1>
        <Link to="/login">Go to login</Link>
        <Link to="/signup">Go to signup</Link>
        </div>
    );
    };

    export default HomePage;

  ```

- Update `app.js`:
  ```js
    import { createBrowserRouter, RouterProvider } from "react-router-dom";
    import HomePage from "./pages/home";
    import LoginPage from "./pages/login";
    import SignupPage from "./pages/signup";

    import "./App.css";

    function App() {
    const router = createBrowserRouter([
        {
        path: "/",
        element: <HomePage />,
        },
        {
        path: "/login",
        element: <LoginPage />,
        },
        {
        path: "/signup",
        element: <SignupPage />,
        },
    ]);

    return <RouterProvider router={router} />;
    }

    export default App;
  ```


### 2. Interactive with S3 <a name='s3'></a>
- Create S3 bucket
  S3 → Create bucket → Input name → Select region → Uncheck `Block all public access` to able access file
    ![s3_1](./img/s3/s3-1.png)

    ![s3_2](./img/s3/s3-2.png)

- Run `npm run build / yarn build` in React app, and upload folder `build` to S3 bucket 
  ![s3_3](./img/s3/s3-3.png)

- After upload success, click tab `Properties` → Scroll down and click `edit` Static web hosting → Enable → Index document (index.html) → save changes
  ![s3_4](./img/s3/s3-4.png)

 - click tab `Permission` at `Bucket policty` click `edit` → paste below code → save changes
  ```json
    {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::demo-deployment-s3-cf/*"
        }
    ]
    }
  ```

- Now, you can access React app by s3 link which displayed at `Properties > Static website hosting > Bucket website endpoint`

### 3. Interactive with Cloudfront <a name='cloudfront'></a>
- Create distribution
  - Select origin domain (s3 url)
  - Origin access, select `Legacy access identities` 
  - Create new OAI
  - Bucket policy, `Yes, update the bucket policy`
  - Update other config if you want
    ![cf_1](./img/cf/cf-1.png)
- After the status shown is `deployed` you can access React app through `Distribution domain name`

### 4. Github actions <a name='github'></a>
- Create file `build-and-deploy.yaml` with structure: `.github/workflows/build-and-deploy.yaml`:
  ```yaml
  name: Build and Deploy React App to CloudFront
  on:
    push:
      branches: [main]
  jobs:
    build-and-deploy:
      name: Build and Deploy
      runs-on: ubuntu-latest
      env:
        BUCKET: <BUCKET NAME>
        DIST: build
        REGION: <YOUR BUCKET REGION>
        DIST_ID: <CLOUDFRONT DISTRIBUTION ID>

      steps:
        - name: Checkout
          uses: actions/checkout@v2

        - name: Configure AWS Credentials
          uses: aws-actions/configure-aws-credentials@v1
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: ${{ env.REGION }}

        - name: Install Dependencies
          run: |
            node --version
            npm install

        - name: Build Static Website
          run: npm run build

        - name: Sync files to S3
          run: |
            aws s3 sync --delete ${{ env.DIST }} s3://${{ env.BUCKET }}

        - name: Create cloudfront invalidation
          run: |
            aws cloudfront create-invalidation \
              --distribution-id ${{ env.DIST_ID }} \
              --paths "/*"
  ```

- To grant access for GitHub Actions to upload files to S3, we need to **create an IAM user** and grant appropriate permissions. Let's create **S3WebAccess** IAM **policy**.

- IAM > Policies > Click Create Policy > Select Tab JSON, copy and paste below policies (*replace your bucket name*):
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListObjectsInBucket",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::<BUCKET_NAME>"
        },
        {
            "Sid": "AllObjectActions",
            "Effect": "Allow",
            "Action": "s3:*Object",
            "Resource": "arn:aws:s3:::<BUCKET_NAME>/*"
        },
        {   
            "Sid": "InvalidateCF",
            "Effect": "Allow",
            "Action": "cloudfront:CreateInvalidation",
            "Resource": "*"
        }
    ]
}
```
![github-1](./img/github/github-1.png)
- Naming, description and click Create policy
![github-2](./img/github/github-2.png)

Create **github-actions** user and attach **S3WebAccess** policy.
  - Naming user, check Access key - Programmatic access.
  ![github-3](./img/github/github-3.png)
  ![github-4](./img/github/github-4.png)
  Tick to `S3WebAccess` → Next: (Tags) → Next (Review) → Create user
  - After creating user sucessfully, you can see `Access key ID` and `Secret access key`, and now we go to github, add them to secrets.
  ![github-5](./img/github/github-5.png)

- Go to GitHub repo and create **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY** secrets:
  - Github > Select repo > settings > secrets > actions > New repository secret
![github-6](./img/github/github-6.png)
![github-7](./img/github/github-7.png)
→ Make any change in the source code, commit and push 
→ Go to tab `Actions` (github), you can see the actions is running.