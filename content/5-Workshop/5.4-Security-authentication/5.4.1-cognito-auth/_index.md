---
title: "Initializing Amazon Cognito User Pool"
date: 2026-08-06
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Create a User Pool in AWS Console

### Step 1: Access Cognito Console

1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/v2/home).
2. In the left navigation menu, select **User pools**.
3. Click **Create user pool** in the top right corner.
![Cognito Sign-in Options](/images/5-Workshop/5.3-Cognito-Auth/5.3.1-signin-options.png)

### Step 2: Name your application

1. Enter your application identifier under **Name your application**.
2. Recommended name: `real-estate-saas-web-app`
3. Naming rule: Maximum **128 characters**, supporting **letters, numbers, spaces**, and special characters `+ = , . @ -`.
![Cognito Set up name](/images/5-Workshop/5.3-Cognito-Auth/5.3.2-set-up-name.png)

### Step 3: Configure sign-in options and attributes

1. Under **Options for sign-in identifiers**, select **Email** to allow users to authenticate using their email address.
2. Under **Self-registration**, check **Enable self-registration** so users can sign up directly from the Next.js frontend.
3. Under **Required attributes for sign-up**, select mandatory user attributes (`email`, `name`).
4. Click **Create user directory** to complete User Pool creation.
![Cognito Configure options](/images/5-Workshop/5.3-Cognito-Auth/5.3.3-configure-options.png)

---

#### 2. Save User Pool ID & Client ID

Once created, copy the **User pool ID** (e.g., `us-east-1_abc123XYZ`) and **App client ID** (e.g., `71a8bc...`), then save them in your backend `.env` configuration file:

```env
AWS_REGION=us-east-1
COGNITO_USER_POOL_ID=us-east-1_abc123XYZ
COGNITO_CLIENT_ID=71a8bc...
```
