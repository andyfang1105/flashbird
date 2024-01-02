# Flashbird Merchant API Documentation

Welcome to the Flashbird API documentation. The Flashbird API allows you to programmatically access and manage shipments, providing a simple way to integrate Flashbird's services into your application.

## Base URL

All URLs referenced in the documentation have the following base:

http://localhost:3001/


The base URL will change depending on the environment (development, staging, production).

## Authentication

To interact with the Flashbird API, you must first authenticate to receive an access token. Use this token in subsequent requests.

### Obtaining an Access Token

**Endpoint:**

POST /ghost/api/v0.1/authentication/token


**Headers:**

- `Content-Type: application/json`

**Body:**

```json
{
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "username": "YOUR_EMAIL_ADDRESS",
  "password": "YOUR_PASSWORD",
  "grant_type": "password"
}
```

Replace YOUR_CLIENT_ID, YOUR_CLIENT_SECRET, YOUR_EMAIL_ADDRESS, and YOUR_PASSWORD with your actual Flashbird API credentials.

Sample JavaScript Request:

```Javascript
const fetch = require('node-fetch');

const data = {
  client_id: 'mock_client_id',
  client_secret: 'mock_client_secret',
  username: 'user@example.com',
  password: 'securepassword',
  grant_type: 'password'
};

async function getAccessToken() {
  try {
    const response = await fetch('http://localhost:3001/ghost/api/v0.1/authentication/token', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const responseData = await response.json();
    console.log('Access Token:', responseData.access_token);
    return responseData.access_token;
  } catch (error) {
    console.error('Fetching error:', error);
  }
}

getAccessToken();
```

**Response:***

The response will include the access_token which you'll use for authenticated requests, a refresh_token to obtain new access tokens once they expire, the duration (expires_in) the access token is valid for, and the type of token.


```json
{
  "access_token": "YOUR_ACCESS_TOKEN",
  "refresh_token": "YOUR_REFRESH_TOKEN",
  "expires_in": 3600,
  "token_type": "Bearer"
}
````

### Making Authenticated Requests
Once you have the access_token, include it in the Authorization header of your requests:

```
headers: {
  'Authorization': 'Bearer YOUR_ACCESS_TOKEN'
}
```

Endpoints
List the available endpoints, their methods, expected input, and output. For example:

## Create Shipment
**Endpoint:**

```
POST /shipments
```

Headers:

```
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN
```

Body:

Describe the required and optional fields for creating a shipment.

Sample Request:

Show a sample request using the fetch library.

Response:

Describe the expected response, including status codes and potential error messages.

Error Handling
Explain how errors are returned and provide a list of possible error codes and what they mean.

Rate Limiting
If applicable, describe any rate limits that apply to the API.

Additional Resources
Provide links to any additional resources such as SDKs, libraries, or community tools related to the API.

Support
Provide contact information or links for where users can get support.











