---
name: Microsoft Graph 
---

[Graph](https://docs.microsoft.com/en-us/graph/overview) is how you access all of Microsoft 365. Starting at the [overview](https://docs.microsoft.com/en-us/graph/api/overview?view=graph-rest-1.0) page, you can see all of the available endpoints you can access through the graph. 

# Registering application 

* Follow the directions [here](https://docs.microsoft.com/en-us/graph/auth-register-app-v2) to register a new application. You will be using Azure portal. 
* After you register, you will need to add some permissions. When you create permissions, you will more then likely be adding permissions for "Microsoft Graph" and not the other Azure type admin permissions. 

How do you know what permissions you need? When you read the graph docs, each endpoint specifies what permissions that are required. [Here](https://docs.microsoft.com/en-us/graph/api/drive-list?view=graph-rest-1.0&tabs=cs#permissions) is an example of that. 
* Next, go to "Certificates and secrets" for your application registered. You will need to create a new Client secret. 
* Now, your Azure admin will need to approve of your application. After they approve, you can authenticate successfully! 

# Javascript/Typescript SDK 

Checkout the SDK [here](https://github.com/microsoftgraph/msgraph-sdk-javascript). 

Make sure you register for an application first before using the SDK. The SDK does some handy things for you such as doing paging and bulk requests. It will also using some code that you provide it to get an access token whenever it needs it so you don't need to worry about refreshing it. 

### Authentication 

* If you want to use Graph within the browser, use [MSAL for authentication](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core). However, for nodejs, do not use it. Instead, you will create your own auth provider. Here is an example of one:

```ts
import { AuthenticationProvider } from "@microsoft/microsoft-graph-client";
import axios from 'axios'
import querystring from 'querystring'

class MyAuthenticationProvider implements AuthenticationProvider {
	/**
	 * This method will get called before every request to the msgraph server
	 * This should return a Promise that resolves to an accessToken (in case of success) or rejects with error (in case of failure)
	 * Basically this method will contain the implementation for getting and refreshing accessTokens
	 */
	public async getAccessToken(): Promise<string> {
		let data = querystring.stringify({
			client_id: "12345678-1234-1234-1234-123456789012",
			scope: "https://graph.microsoft.com/.default",
			client_secret: "<client-secret-here>",
			grant_type: "client_credentials"
		})

        let tenantId = "12345678-1234-1234-1234-123456789012"
		return axios.post(`https://login.microsoftonline.com/${tenantId}/oauth2/v2.0/token`, data, {
			headers: {
				'Content-Type': 'application/x-www-form-urlencoded',
				'Host': 'login.microsoftonline.com'
			}
		}).then((response): Promise<string> => {			
			return Promise.resolve(response.data.access_token)
		}).catch((error) => {			
			throw error 
		})
	}
}
```

The `client_id`, `client_secret` and `tenantId` you get after registering your Azure application. 

* Now, when you use the SDK, it will automatically add the access token for you for each request. 
