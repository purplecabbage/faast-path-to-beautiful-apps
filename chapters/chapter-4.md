# Chapter 4. Call Target from Runtime

## Learning Objectives

- Write an action that makes an API call to Adobe Target

## Lab Tasks

- Add Target as a Service
- Create Runtime action to make an API call to Target
    - Create an action that gets an Access Token for Target
    - Make a call to Target

---

## 1. Add Target as a Service
**GOAL:** We will add Target as a service in your integration

1. Navigate to Adobe I/O Console at [https://console.adobe.io](https://console.adobe.io) in your browser
1. Find the Integration that you created during Chapter 1 and open it
1. Select the `Services` tab in your Integration
1. Under `Available Services`, select `Adobe Target`
    ![](../images/chapter-4/1.png)
1. Under `Adobe Target Configuration`, select `Default Workspace`
    ![](../images/chapter-4/2.png)
1. Click `Add Service` to complete 
1. You should now see 2 services under `Configured Services`, Adobe Target and I/O Management API
    ![](../images/chapter-4/3.png)

## 2. Create Runtime action to make an API call to Target
**GOAL:** We will start with an action that gets an access token for Target, then add an API call to the action so that your action can create a new profile in Target.

### Set up an action that gets an Access Token for Target
Any API that accesses a service or content on behalf of an end user authenticates using the OAuth and JSON Web Token standards. For service-to-service integrations, you will also need a JSON Web Token (JWT) that encapsulates your client credentials and authenticates the identity of your integration. You exchange the JWT for the OAuth token that authorizes access.

For more details, please refer to [Service Account Integration documentation](https://www.adobe.io/apis/cloudplatform/console/authentication/jwt_workflow.html)

The JWT Workflow contains 6 steps
- 1- Create a Public Key Certificate
- 2- Subscribe to a Service or Event Provider
- 3- Configure a Service Account Integration
- 4- Secure your Client Credentials
- 5- Create your JSON Web Token (JWT)
- 6- Exchange your JWT for an Access Token

Let's set up an action that helps you create and store an Access Token in Runtime
1. In the resources you have downloaded, navigate to `adobeio-cna-lib-auth-ims-master` folder
    ```
    $ cd ~/Desktop/adobeio-cna-lib-auth-ims-master/
    ```
    For Git users, you can also get the latest copy off our [GitHub repo](https://github.com/adobe/adobeio-cna-lib-auth-ims).
1. We'll create a `.env` file to configure this library to your integration. Run the following commands
    ```
    $ touch .env
    $ open .env
    ```
1. Copy the following block in as place holder:
    ```
    OW_NAMESPACE="<change-me>"
    # Optional parameters (should use 'npm run configure' if used)
    IMS_AUTH_TYPE=<code/jwt>
    ```
1. You can find the first two values using your Adobe I/O CLI
    ```
    $ aio runtime property get
    ```
    Update `OW_NAMESPACE` in the `.env` file to match to your `whisk namespace`. 
1. Save your `.env` file. 
1. Now, we'll also populate the `jwt.json` file. 
    ```
    $ open jwt.json
    ```
    Most values can be found in the `Overview` tab in Console. The metascope can be seen in your JWT tab, it should look something like this
    ```
    "meta_scopes": ["https://ims-na1.adobelogin.com/s/ent_adobeio_sdk", "https://ims-na1.adobelogin.com/s/ent_marketing_sdk"]
    ```
    ![9](../images/9.png)
1. Next, run the following command,
    ```
    awk -v ORS='\\n' '1' ~/Desktop/private.key | pbcopy
    ```
    The private key has now been copied to your clipboard. Paste it into `jwt_secret` field. Starting from ```-----BEGIN PRIVATE KEY-----``` to ```-----END PRIVATE KEY-----```.
1. Save your `jwt.json` file. 
1. Your AUTH library is now set up. Let's deploy it into your namespace by running the following command. 
    ```
    $ npm install
    $ npm run deploy-jwt
    ```
    Your should get something that looks like
    ```
    endpoints (web actions): https://https://adobeioruntime.net/api/v1/web/<YOUR_NAMESPACE>/jwtauthp/jwtauthenticate
    ```
    
### Run the action to get an Access Token
1. You now have 2 new packages for Auth in your namespace. You can look them up by
    ```
    $ aio runtime package list
    ```
    You should see
    ```
    /NAMESPACE/jwtauthp-shared                      private 
    /NAMESPACE/jwtauthp                             private 
    ```
1. Now, let's invoke the Auth aciton to get a token
    ```
    $ aio runtime action invoke jwtauthp/jwtauthenticate --blocking
    ```
    You can see that this is actually a sequence, and has invoked multiple actions.
1. You can now look up your access token at 
    ```
    $ aio runtime action invoke jwtauthp-shared/jwtauth --result
    ```
    You should see your access token returned.  
    ```
    {
      "accessToken": "xxxxxxxxxxxxxxxxxxx",
      "accessTokenExpiry": xxxxxxxxxxxxxxxxxxx
    }
}

## 3. Create Runtime action to make an API call to Target

**Challenge:** Let's sequence this action with a Target API call. There's a sample action on your Desktop `target.js`. Can you figure out how to use the access token you just create to make an API call to Target? (Hint: default parameter and sequences are your best friends.)

If you got it right, the result should look something similar to
```
{
  "result": {
    "limit": 2147483647,
    "offset": 0,
    "total": 0
  }
}
```

---
### Navigate

| **Next:**                                                        |
| ---------------------------------------------------------------- |
| Lesson 5 - [Use Project Starter to build an app with UI](chapter-5.md) |

**Return Home:** [Workbook Index](../README.md)
