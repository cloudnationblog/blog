---
title:  Okta SSO for Ansible Semaphore
last_modified_at: 2023-08-06
categories: ["DevOps"]
description: Seamlessly integrate Ansible projects into your CI/CD practices using Ansible Semaphore. Learn how to leverage Okta as an OIDC provider, enabling secure authentication and user management. Configure Semaphore effortlessly with step-by-step instructions, empowering your team with enhanced access control and collaboration.
header:
   teaser: /images/blog/semaphore-okta-sso.png
   og_image: /images/blog/semaphore-okta-sso.png
---

[Ansible Semaphore](https://www.ansible-semaphore.com/) offers a user-friendly approach for transitioning an established Ansible project towards the adoption of CI/CD methodologies.

As of [PR #1213](https://github.com/ansible-semaphore/semaphore/pull/1213), Semaphore extends its support beyond LDAP by incorporating OIDC as an additional identity protocol. Integrating Semaphore with Okta using OIDC is a straightforward process.

## Okta Configuration

1. Login to the Okta Admin portal.
2. Within the **Applications** section, create a new app integration.
{% include lightbox.html src="okta-semaphore-app-integration.png" data="group" %}
3. Choose `OIDC - OpenID Connect` followed by `Web Application` from the available options.
4. On the configuration page:
   - App integration name: `Ansible Semaphore`
   - Grant Type: `Authorization Code`
   - Sign-in redirect URIs: `<your_semaphore_http/s_address>/api/auth/oidc/okta/redirect`. Example: `https://semaphore.cloudnation.blog/api/auth/oidc/okta/redirect`. (Semaphore is behind a reverse-proxy in this case.)
   - Sign-out redirect URIs: null
   - Controlled Access: Skip group assignment for now
5. Save.
6. Under the `General` tab you will find the `Client ID` and `Client Secrets`. These values are required for the subsequent Semaphore configuration procedures.

Semaphore requires the OIDC provider URL for Okta. To obtain this URL, navigate to *Security > API > Authorization Server*, note the `Issuer URI` for the `default` authorization server.

{% include lightbox.html src="okta-authorization-server.png" data="group" %}

## Semaphore Configuration

1. Update the `oidc_providers` key in `config.json` as below:
   - `provider_url`: Must match the unique Okta tenant URI as in the screenshot above.
   - `client_id`: Client ID obtained from Step 6 of the Okta configuration.
   - `client_secret`: Client Secret obtained from Step 6 of the Okta configuration.
   - `name_claim`: By default, the `username` and the `name` are both set to the same claim(attribute) -> `preferred_username`. This configuration will generate a Semaphore user with both the username and display name typically reflecting the user's email.. To use the Okta display name as the display name on semaphore, this key can be set to use the `name` attribute sent by Okta.
   ```json
   {
   "oidc_providers": {
      "okta": {
         "display_name": "Sign in with Okta",
         "provider_url": "https://dev-xxxxx.okta.com/oauth2/default",
         "client_id": "0oaaor46qeG99JPDD5d7",
         "client_secret": "--rOEm_9Vy8bxZ7i2EwsjoWYEAhkS5wWJPyM7mKwuEdk6L8-YswpWxegka_pwNCL",
         "name_claim": "name"
      }
   }
   }
   ```
2. Restart Semaphore.
3. Browse to the Semaphore web page and it should now have a button to **SIGN IN WITH OKTA**.
{% include lightbox.html src="semaphore-okta-sso.png" data="group" %}

The sign-in process will re-direct to Okta and then back to Semaphore. Upon successful authentication, a new user will be created automatically for the validated username, if it does not already exist. Following user creation, an administrator can then assign the requisite permissions to the newly established user account.

## Suggested Configuration Parameters

`password_login_disable`: Disable local user/password authentication. Only LDAP and OpenID. This can be useful to implement after federation is setup to enforce SSO/MFA.

`non_admin_can_create_project`: Set to `false` to block any new users from being able to create projects.

`web_host`: Set this to the user-facing URL of the web instance. Example: `https://semaphore.cloudnation.blog`.
