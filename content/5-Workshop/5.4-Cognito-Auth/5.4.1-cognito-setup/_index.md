---
title: "Initialize Amazon Cognito User Pool"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Create a User Pool via AWS Console

### Step 1: Access and Initialize
1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/v2/home).
2. On the left navigation menu, select **User pools**.
3. Click the **Create user pool** button in the top right corner to start the configuration process.

![Cognito Sign-in Options](/images/5-Workshop/5.3-Cognito-Auth/5.3.1-signin-options.png)

### Step 2: Name your application
1. Enter your representative application name under **Name your application**.
2. Proposed name examples: `real-estate-saas-web-app` or `My SPA app - real-estate`
3. Rules: Maximum **128 characters**, accepts **letters, numbers, spaces**, and special characters `+ = , . @ -`.

![Overview](/images/5-Workshop/5.3-Cognito-Auth/5.3.2-set-up-name.png)

### Step 3: Configure
1. Under **Options for sign-in identifiers**, check the **Email** checkbox to allow users to sign in with their email address.
2. Under **Self-registration**, check **Enable self-registration** to allow users to register an account from the application interface.
3. Under **Required attributes for sign-up**, click the **Select attributes** dropdown and choose required attributes users must provide upon sign-up (e.g., `email`, `name`).
4. *(Optional)* Under **Add a return URL - optional**, enter the URL path where Cognito will redirect users back to the application after successful sign-in via Hosted UI.
5. Click **Create user directory** button at the bottom right to complete Cognito User Pool initialization.

![Overview](/images/5-Workshop/5.3-Cognito-Auth/5.3.3-configure-options.png)

---

#### 2. Note Down Pool ID & Client ID

Once created, copy the **User pool ID** (e.g., `us-east-1_abc123XYZ`) and **App client ID** (e.g., `71a8bc...`), then add them to your `apps/server/.env` and `apps/client/.env`:

```env
AWS_REGION=us-east-1
COGNITO_USER_POOL_ID=us-east-1_abc123XYZ
COGNITO_CLIENT_ID=71a8bc...
```
