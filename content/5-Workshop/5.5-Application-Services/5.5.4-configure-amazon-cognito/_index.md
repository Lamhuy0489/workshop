---
title: "Configure Amazon Cognito & Google OAuth 2.0"
date: 2026-09-24
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

### Hands-On Objectives

Configure a centralized cloud identity management architecture utilizing **Amazon Cognito User Pool** federated with **Google OAuth 2.0 Identity Provider**. This enables seamless, secure Single Sign-On (SSO) authentication for the Web Studio platform without storing plaintext credentials on application hosts.

---

## 1. Architecture Overview: Amazon Cognito & Federated Identity

Amazon Cognito delivers enterprise-grade identity directories, federation, and access control:
* **Amazon Cognito User Pool (`huylam-ocr-user-pool` / `User pool - qp0rmn`)**:
  * Acts as the authoritative identity directory, managing user lifecycle, self-registration, password recovery, and issuing standard OIDC / JWT security tokens (`id_token`, `access_token`, `refresh_token`).
  * Features a dedicated Cognito authentication domain: `https://kc4iwg.auth.ap-southeast-1.amazoncognito.com`.
* **Social Identity Federation (Google OAuth 2.0)**:
  * Facilitates seamless one-click sign-in via Google accounts.
  * Cognito handles the OpenID Connect redirection flow (`/oauth2/idpresponse`), maps inbound profile claims (`email` and `sub`), and auto-provisions user profiles in the pool.
* **FinOps Governance (Zero Dollar Cost)**:
  * Leverages AWS Cognito Free Tier providing 50,000 Monthly Active Users (MAU) permanently free of charge.

---

## 2. Step-by-Step Implementation on Google Cloud and AWS Console

### Step 2.1: Configure OAuth 2.0 Client on Google Cloud Console
1. Navigate to **Google Cloud Console -> APIs & Services -> Credentials**.
2. Select **+ Create credentials -> OAuth client ID**:

![Create OAuth Client ID on Google Cloud Console](/images/cognito/01-google-console-oauth-credentials.png)

3. Set up client attributes:
   * **Application type**: Web application.
   * **Name**: Web client 1.
   * **Authorized JavaScript origins**:
     * `http://localhost:5000`
     * `http://127.0.0.1:5000`
     * `http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com`
   * **Authorized redirect URIs**:
     * `https://kc4iwg.auth.ap-southeast-1.amazoncognito.com/oauth2/idpresponse`
4. Click **Save** and securely record the generated **Client ID** and **Client secret**:

![Google Console Redirect URI and Client Secrets](/images/cognito/06-google-console-redirect-uri-saved.png)

---

### Step 2.2: Provision User Pool on Amazon Cognito Console
1. Log in to the AWS Console in region **ap-southeast-1 (Singapore)**.
2. Navigate to **Amazon Cognito -> Create user pool** (or use the application resource setup wizard).
3. Specify the foundational settings:

| Attribute | Configured Value | Technical Purpose |
| :--- | :--- | :--- |
| **Application type** | `Traditional web application` | Server-hosted web application (Gunicorn / Flask) |
| **Name your application** | `huylam-ocr-user-pool` | User directory identifier |
| **Options for sign-in identifiers** | `Email` | Authentication via email address |
| **Self-registration** | `Enable self-registration` | Allow users to register accounts autonomously |

![Specify Application Parameters on Amazon Cognito](/images/cognito/02-cognito-setup-application-screen.png)

4. Acknowledge external federated sign-in support (Social, SAML, or OIDC sign-in):

![Cognito Social and OIDC Sign-In Dialog](/images/cognito/03-cognito-social-signin-info-popup.png)

5. Complete the setup. The user pool `User pool - qp0rmn` and application client `huylam-ocr-user-pool` are created:

![Amazon Cognito User Pool Successfully Provisioned](/images/cognito/04-cognito-user-pool-created-success.png)

---

### Step 2.3: Verify Sign-in Experience Configuration
1. Open the **Sign-in** tab within the user pool console to verify settings:
   * **Cognito user pool sign-in**: Email identifier enabled.
   * **Options for choice-based sign-in**: Password authentication supported.
   * **Device tracking & Account recovery**: Self-service email recovery active.

![Cognito Sign-in Experience Overview](/images/cognito/05-cognito-sign-in-experience-tab.png)

---

### Step 2.4: Integrate Google as a Federated Identity Provider
1. From the left navigation pane, select **Social and external providers -> Add identity provider**.
2. Choose **Google** and populate credentials:
   * **Client ID**: `73148288621-r8m9svnv8iiisb6qr084to7fsrivvoc7.apps.googleusercontent.com`
   * **Client secret**: Stored secret key from Google Cloud Console.
   * **Authorized scopes**: `profile email openid`
3. Map user attributes (**Attribute mapping**):
   * `email` (User pool attribute) <-> `email` (Google attribute)
   * `username` (User pool attribute) <-> `sub` (Google attribute)
4. Click **Save changes**. The Google Identity Provider is activated:

![Google Identity Provider Configured in Cognito](/images/cognito/07-cognito-identity-provider-google-created.png)

---

### Step 2.5: Verify Web Studio Single Sign-On Ingress
1. Navigate to the Web Studio login page via ALB endpoint (`http://huylam-ocr-alb-1284818160.ap-southeast-1.elb.amazonaws.com/login`) or local host:
2. The **Sign in with Google (AWS Cognito)** action button renders with official Google branding.
3. Clicking the button initiates secure OIDC authorization and authenticates the user directly into Web Studio:

![Web Studio Login Interface Integrating AWS Cognito](/images/cognito/08-web-studio-cognito-login-screen.png)

---

## 3. Expected Outcomes

Upon completing this module, you will have:
- An active **Amazon Cognito User Pool** in Singapore (`ap-southeast-1`).
- Seamless **Google OAuth 2.0 Federation** with automated claim mapping.
- A multi-channel login portal supporting both local credentials and Google SSO.
- Zero server-stored secrets, maintaining complete FinOps alignment at $0.00 operational cost.
