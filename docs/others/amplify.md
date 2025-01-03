---
label: AWS Amplify
title: A DEVELOPER'S GUIDE TO HOSTING NEXTJS APP ON AWS AMPLIFY
---

## 1. Introduction

- AWS Amplify is everything frontend developers need to develop and deploy cloud-powered fullstack applications without hassle. Easily connect your frontend to the cloud for data modeling, authentication, storage, serverless functions, SSR app deployment, and more.
- You can use AWS Amplify to build, connect, and host fullstack apps on AWS: React, Next.js, Angular.

## 2. Concepts

- uses a TypeScript-based, code-first developer experience (DX).
- use Amplify for end-to-end fullstack development developer.
- can use cloud sandbox environments to get an isolated cloud development environment for each developer.

Learn more about [Concepts](https://docs.amplify.aws/react/how-amplify-works/concepts/)

## 3. Deploy NextJS SSR application

Go to [AWS Amplify Console](https://ap-southeast-1.console.aws.amazon.com/amplify/apps) click button `Create new app` to create new amplify application.
![amplify-console](https://github.com/user-attachments/assets/3722b64e-0820-40f1-be11-c6e9781507fa)

### 3.1 Choose source code provider

![amplify-provider](https://github.com/user-attachments/assets/11660129-99df-423b-a678-3bf5796750e9)

Choose `GitHub` and click `Next`. A popup will appear, requiring you to grant Amplify access to the repository you want to deploy.

### 3.2 Add repository and branch

Select a `repository` and a `branch` you want to deploy, then click `Next`.

![amplify-repo](https://github.com/user-attachments/assets/0c410cbe-3154-4fc9-bccd-817b7328e726)

### 3.3 App settings

Review the App name and Build settings, and modify them if needed:

- **App name**: Default value is derived from the GitHub repository.
- **Frontend build command**: For example, yarn run build.
- **Build output directory**: For example, .next, .out, .build, etc.

![amplify-setting](https://github.com/user-attachments/assets/4e5a4c5e-d283-4563-88e3-59ac26baef4a)

**Service role**: Amplify requires permissions to publish Server-Side Rendering (SSR) logs to your CloudWatch account. Please create a new service role or use an existing one.
![amplify-service-role](https://github.com/user-attachments/assets/1b801a78-2a3b-48da-9741-c85115494ef1)

**Advanced settings:**

If your project uses environment variables:

- Add all variables in the `Environment variables` section.
![amplify-advance](https://github.com/user-attachments/assets/ff7450cb-8b53-47ff-b504-b1e02bf42c09)

- Back to `Build settings` and update the `Build commands` configuration by clicking the Edit YML File button. Example:

```yml
version: 1
frontend:
    phases:
        preBuild:
            commands:
                - 'yarn install --frozen-lockfile'
        build:
            commands:
                - 'env | grep -e DOMAIN -e FIREBASE_ADMIN_CLIENT_EMAIL -e FIREBASE_ADMIN_PRIVATE_KEY -e AUTH_COOKIE_NAME -e AUTH_COOKIE_SIGNATURE_KEY_CURRENT -e AUTH_COOKIE_SIGNATURE_KEY_PREVIOUS -e USE_SECURE_COOKIES >> .env.production'
                - 'env | grep -e NEXT_PUBLIC_ >> .env.production'
                - 'yarn build'
    artifacts:
        baseDirectory: .next
        files:
            - '**/*'
    cache:
        paths:
            - '.next/cache/**/*'
            - 'node_modules/**/*'
```

 The build commands section writes environment variables to the `.env.production` file before building the application.

### 3.4 Review

![amplify-review](https://github.com/user-attachments/assets/00e16e4c-c207-435e-9a44-60b764ea68f9)

Review all information:

- Repository details
- App settings
- Advanced settings

=> Once everything is confirmed, click `Save and deploy`.

### 3.5 Management

- **Manage application overview**: View and manage hosting and app settings.
![amplify-review](https://github.com/user-attachments/assets/493f089e-6f26-4320-b7ff-aa41a1e844e7)
- **Deployment versions**: View all deployment versions for a specific branch by selecting it on the overview screen.
![amplify-view-version](https://github.com/user-attachments/assets/f395afc7-7838-44fe-9496-d1ac11408b71)
- **Deployment logs**: Access detailed deployment logs for debugging and monitoring.
![amplify-view-log](https://github.com/user-attachments/assets/ef821c9a-9eed-4aae-8a73-6de5831e67bf)

## 4. Deploy NextJS SSG application

Refer to the [AWS Amplify documentation](https://docs.aws.amazon.com/amplify/latest/userguide/deploy-nextjs-app.html#build-setting-detection-ssg-14) for detailed instructions on deploying an SSG application.

**Note**: In the `amplify.yml` configuration file, ensure the `baseDirectory` is set to the `.next` folder instead of the `out` folder, as used during local development.

![yaml config](https://github.com/user-attachments/assets/c103bba2-0201-48a1-96b1-e682ebe08997)

**Deployment Steps:** Follow 4 steps as deploy SSR Application.

View result:
![amplify-static-export](https://github.com/user-attachments/assets/e391d32f-6f71-408e-b3cb-c0880fd61a8a)

## 5. Amazon S3 vs. AWS Amplify Comparison

### 5.1. Amazon S3

- Pros:
  - **Cost-Efficiency**: only pay for the storage you use and the data transfer out, making it a budget-friendly option.
  - **Scalability**: S3 automatically scales to accommodate large amounts of traffic without any configuration needed.
  - **Performance**: Coupled with Amazon CloudFront, S3 can serve content quickly from edge locations worldwide, reducing latency.
  - **Integration with Other Services**: Directly integrate with other AWS services like Lambda@Edge or S3 Event Notifications.
  - **Independence from Frameworks**: S3 + CloudFront works for any static site, not just Next.js, making it more flexible.

- Cons:
  - **Static Content Only**: S3 is designed for static files; Need to implement AWS Lambda or another backend service for dynamic functionality.
  - **Manual Setup:**
    - Requires manually configuring S3 buckets, permissions, and CloudFront distributions.
    - No automated pipeline for source code, builds, or deployments.
  - **No Built-in CI/CD**: You'll need to implement CI/CD workflows using third-party tools like GitHub Actions, AWS CodePipeline, or other automation services.
  - **Complex Setup for Custom Domains**: While you can host custom domains on S3, the initial setup may require additional steps involving Route 53 and CloudFront.

### 5.2 Amazon Amplify

- Pros:
  - **Ease of Deployment**: Amplify automates the deployment pipeline, from pulling the source code to building and deploying the app.
  - **CI/CD Integration**: Built-in support for Git-based workflows (e.g., GitHub, GitLab, Bitbucket). Automatically redeploys when changes are pushed to the repository.
  - **Out-of-the-Box Features:**
    - Environment variables management.
    - Logging through CloudWatch.
    - Custom domain configuration with automatic SSL certificate management.
    - Automatic routing for multiple environments (e.g., staging, production).

- Cons:
  - **Vendor Lock-In**: Tightly coupled with AWS, making migration to other providers more challenging.
  - **Limited Control:**: Some configurations (e.g., caching policies or deployment structure) are less customizable compared to S3 + CloudFront.
  - **Cost for Small Projects**: While Amplify's pricing is predictable, it might be more expensive than S3 for simple, static deployments.
