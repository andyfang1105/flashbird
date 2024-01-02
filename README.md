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

Response:

The response will include the access_token which you'll use for authenticated requests, a refresh_token to obtain new access tokens once they expire, the duration (expires_in) the access token is valid for, and the type of token.



