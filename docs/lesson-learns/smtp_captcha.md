## WORDPRESS SMTP, CAPTCHA SETUP INSTRUCTION

### 1. SMTP
Go to console.cloud.google.com create new project.
![image](./img/smtp/create.png)

![image](./img/smtp/new.png)

![image](./img/smtp/1.png)

Next, enable the Gmail AP. In your Google Cloud Console sidebar, go to: APIs & Services » Library.

Then look under Category and click on Google Workspace, or enter **Gmail API** in the search bar.

![image](./img/smtp/2.png)

On the Gmail API page, click on the blue ENABLE button.
![image](./img/smtp/3.png)

click on the CREATE CREDENTIALS button

![image](./img/smtp/4.png)

From the Select an API dropdown, choose Gmail API → click Next

![image](./img/smtp/5.png)

- **App name**: Enter an app name of your choice.
- **User support email**: Select your email address from the choices provided.
- **App logo**: If you’d like, you can upload a logo for your app. This is optional.
  
![image](./img/smtp/6.png)

Next, you’ll need to fill out some information about your OAuth Client ID.
![image](./img/smtp/OAuth.png)

Next, skip the Authorized JavaScript origins section and scroll to Authorized redirect URIs.
![image](./img/smtp/7.png)

Click on the + ADD URI button and input the following: https://connect.wpmailsmtp.com/google/. 
→ CREATE
![image](./img/smtp/8.png)

Under User type, click on the **MAKE EXTERNAL** button.

![image](./img/smtp/9.png)

Click Confirm
![image](./img/smtp/10.png)

Next, click on **Credentials** in the left side menu.
To view the **Client ID** and **Client Secret**, click the **pencil** icon.

![image](./img/smtp/11.png)

On the right side of this page, you’ll see the **Client ID** and **Client secret** values.

![image](./img/smtp/12.png)

Go ahead and copy both of these values into the corresponding fields in your WP Mail SMTP settings → click the Save Settings button 
![image](./img/smtp/13.png)

You must grant permission before Google allows our plugin to use your Gmail API to send emails.

Scroll to the bottom of the WP Mail SMTP settings page → click the button **Allow plugin to send emails using your Google account**
→ Click the Allow button to proceed
![image](./img/smtp/14.png)

You’re ready to send a test email. To do so, navigate to **WP Mail SMTP** » Tools and open up the Email Test tab.

![image](./img/smtp/15.png)

### 2. Captcha

Sign in to Google with the account → go to [reCAPTCHA Admin Console](https://www.google.com/recaptcha/admin/create)

After that, Choose **reCAPTCHA v3** from type options, and enter the domain of the website in the Domains field.

![image](./img/smtp/16.png)

After you register a website, you will get the **site key** and **secret key** for the site.

![image](./img/smtp/17.png)

Next, move to the WordPress admin screen and open the **Contact** > **Integration** menu page.

Copy-paste the two keys you received in the previous step into the fields, and click Save Changes.
![image](./img/smtp/18.png)

Now your contact forms use **reCAPTCHA’s** score to verify whether the form submission is from a human or from a spam bot

![image](./img/smtp/19.png)