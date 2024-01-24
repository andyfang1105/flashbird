# Flashbird Merchant API Documentation

Welcome to the Flashbird API documentation. The Flashbird API allows you to programmatically access and manage shipments, providing a simple way to integrate Flashbird's services into your application.

## Table of Contents

- [Base URL](#base-url)
- [Authentication](#authentication)
  - [Obtain Access Token and Refresh Token from Flashbird Merchant Console](#obtain-access-token-and-refresh-token-from-flashbird-merchant-console)
  - [Obtain New Access Token via Refresh Token](#obtain-new-access-token-via-refresh-token)
- [Making Authenticated Requests](#making-authenticated-requests)
- [Endpoints](#endpoints)
  - [Create Shipment](#create-shipment)



## Base URL

All URLs referenced in the documentation have the following base:

http://localhost:3001/api/2024-01/merchant


The base URL will change depending on the environment (development, staging, production).

## Authentication

To interact with the Flashbird API, you must first authenticate to receive an access token. Use this token in subsequent requests.

### Obtain Access Token and Refresh Token from Flashbird Merchant Console

![Flashbird Merchant Console](images/merchant_api_console.jpg "Flashbird Merchant Console")

To get your access token and refresh token directly from the Flashbird Merchant Console:

1. Log in to the Flashbird Merchant Console.
2. Ensure the Merchant API is enabled for your account.
3. Click the "Reset Token" button.
4. Enter your username and password in the popup dialog.
5. Your access token and refresh token will be displayed.

**Note:**
- The access token is valid for 1 hour. If it expires, you can use the refresh token to obtain a new access token.
- The refresh token is valid for 1 week and will be automatically renewed whenever you use it to obtain a new access token via the method described in the "Obtain New Access Token via Refresh Token" section.



### Obtain New Access Token via Refresh Token 

If your access token has expired and the API function returns a 401 status code, indicating unauthorized access due to an invalid or expired token, use the `refresh_token` obtained during the initial authentication to request a new access token.

**Endpoint:**
```
POST /authentication/token
```

**Headers:**
```
Content-Type: application/json
```

**Body:**
```json
{
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "grant_type": "refresh_token",
  "refresh_token": "YOUR_REFRESH_TOKEN"
}
```

**Sample JavaScript Request:**
```javascript
const fetch = require('node-fetch');
const BASE_URL = 'http://localhost:3001/api/2024-01/merchant'; // Replace with actual base URL

const data = {
  client_id: 'mock_client_id',
  client_secret: 'mock_client_secret',
  grant_type: 'refresh_token',
  refresh_token: 'mock_refresh_token'
};

async function refreshAccessToken() {
  try {
    const response = await fetch(`${BASE_URL}/authentication/token`, {
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
    console.log('New Access Token:', responseData.access_token);
    return responseData.access_token;
  } catch (error) {
    console.error('Fetching error:', error);
  }
}

refreshAccessToken();

```

**Response:**

A successful response will return a new `access_token`, the duration it's valid for (`expires_in`), and the type of token (`token_type`).

```json
{
  "access_token": "NEW_ACCESS_TOKEN",
  "expires_in": 3600,
  "token_type": "Bearer"
}
```




## Making Authenticated Requests
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

**Headers:**

```json
{
  "Content-Type": "application/json",
  "Authorization": "Bearer YOUR_ACCESS_TOKEN"
}
```
Replace `YOUR_ACCESS_TOKEN` with the actual access token obtained from the authentication process.


**Body:**

Provide the shipment details in the request body. The following fields are required: `name`, `phone`, `street`, `city_or_town`, `province`, `post_code`, `country` in both from and to sections. The `packaging` is an array where each item must include at least the weight.

```json
{
  "from": {
    "name": "Alice Smith",
    "phone": "(416) 555-0190",
    "street": "123 Yonge Street",
    "city_or_town": "Toronto",
    "province": "ON",
    "post_code": "M5B 1N8",
    "country": "Canada"
  },
  "to": {
    "name": "Bob Brown",
    "phone": "(416) 555-0132",
    "street": "456 Queen Street West",
    "city_or_town": "Toronto",
    "province": "ON",
    "post_code": "M5V 2B7",
    "country": "Canada"
  },
  "packaging": [
    {
      "length": "10",
      "width": "15",
      "height": "20",
      "weight": 5
    }
  ],
  "refno": "1234567"
}
```

**Sample JavaScript Request:**

```javascript
const fetch = require('node-fetch');
const BASE_URL = 'http://localhost:3001/api/2024-01/merchant'; // Replace with actual base URL
const endpoint = 'shipments'; // The endpoint for creating shipments
const accessToken = 'YOUR_ACCESS_TOKEN'; // Replace with your actual access token

const shipmentData = {
  "from": {
    "name": "Alice Smith",
    "phone": "(416) 555-0190",
    "street": "123 Yonge Street",
    "city_or_town": "Toronto",
    "province": "ON",
    "post_code": "M5B 1N8",
    "country": "Canada"
  },
  "to": {
    "name": "Bob Brown",
    "phone": "(416) 555-0132",
    "street": "456 Queen Street West",
    "city_or_town": "Toronto",
    "province": "ON",
    "post_code": "M5V 2B7",
    "country": "Canada"
  },
  "packaging": [
    {
      "length": "10",
      "width": "15",
      "height": "20",
      "weight": 5
    }
  ],
  "refno": "1234567"
};

async function createShipment() {
  try {
    const response = await fetch(`${BASE_URL}/${endpoint}`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${accessToken}`
      },
      body: JSON.stringify(shipmentData),
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const responseData = await response.json();
    console.log('Response Data:', responseData);
  } catch (error) {
    console.error('Fetching error:', error);
  }
}

createShipment();
```

**Response:**

The response will provide details about the success or failure of the shipment creation.

- **When Successful (rc is 0):**
  ```json
  {
    "rc": 0,
    "number": "UNIQUE_SHIPMENT_NUMBER",
    "refno": "YOUR_REFERENCE_NUMBER",
    "message": "Successfully created a shipment",
    "timestamp": 1704152543232
  }
  
  ```
- **When Failed (rc is -1):**

  Duplicate Reference Number Example:

  ```json
  {
    "rc": -1,
    "refno": "YOUR_REFERENCE_NUMBER",
    "number": "",
    "message": "Failed to create the shipment due to duplicate Ref No. (YOUR_REFERENCE_NUMBER) found in shipment UNIQUE_SHIPMENT_NUMBER",
    "timestamp": 1704152662478
  }
  
  ```

  Out of Service Area Example:

  ```json
  {
    "rc": -1,
    "refno": "YOUR_REFERENCE_NUMBER",
    "number": "",
    "message": "FSA/Postal Code A8S is out of service area",
    "timestamp": 1704152679730
  }
  ```



## Update Shipment
**Endpoint:**

```
PUT /shipments/{shipmentNumber}
```

**Headers:**

```json
{
  "Content-Type": "application/json",
  "Authorization": "Bearer YOUR_ACCESS_TOKEN"
}
```
Replace `YOUR_ACCESS_TOKEN` with the actual access token obtained from the authentication process.


**URL Parameters:**

shipmentNumber: The unique tracking number of the shipment to be updated.

**Body:**
Provide the updated shipment details in the request body. The same fields as in the shipment creation are applicable, but only the fields that are being updated need to be included.

```json
{
  "from": {
    "name": "Alice Smith",
    "phone": "(416) 555-0190",
    "street": "123 Yonge Street",
    "city_or_town": "Toronto",
    "province": "ON",
    "post_code": "M5B 1N8",
    "country": "Canada"
  },
  "to": {
    "name": "Bob Brown",
    "phone": "(416) 555-0132",
    "street": "456 Queen Street West",
    "city_or_town": "Toronto",
    "province": "ON",
    "post_code": "M5V 2B7",
    "country": "Canada"
  },
  "packaging": [
    {
      "length": "10",
      "width": "15",
      "height": "20",
      "weight": 5
    }
  ],
  "refno": "1234567"
}
```

**Sample JavaScript Request:**
```javascript
const fetch = require('node-fetch');
const BASE_URL = 'http://localhost:3001/api/2024-01/merchant'; // Replace with actual base URL
const accessToken = 'YOUR_ACCESS_TOKEN'; // Replace with your actual access token
const shipmentNumber = '774017844640'; // Replace with the actual shipment number to be updated

const updatedShipmentData = {
  // Include updated shipment data here
};

async function updateShipment() {
  try {
    const response = await fetch(`${BASE_URL}/shipments/${shipmentNumber}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${accessToken}`
      },
      body: JSON.stringify(updatedShipmentData),
    });

    if (!response.ok) {
      const errorResponse = await response.json();
      throw new Error(`HTTP error! status: ${response.status}, message: ${errorResponse.errors[0].message}`);
    }

    const responseData = await response.json();
    console.log('Response Data:', responseData);
  } catch (error) {
    console.error('Fetching error:', error);
  }
}

updateShipment();
```
**Response:**


- **When Successful (response.ok and rc is 0):**
```json
{
  "rc": 0,
  "number": "UPDATED_SHIPMENT_NUMBER",
  "msg": "Shipment updated",
}
```
- **When Failed (response.ok is not true):**
  Check ${response.status} and ${errorResponse.errors[0].message}


## Delete Shipment
**Endpoint:**

```
DELETE /shipments/{shipmentNumber}
```

**Headers:**

```json
{
  "Content-Type": "application/json",
  "Authorization": "Bearer YOUR_ACCESS_TOKEN"
}
```
Replace `YOUR_ACCESS_TOKEN` with the actual access token obtained from the authentication process.

**URL Parameters:**
shipmentNumber: The unique identifier of the shipment to be deleted.

**Sample JavaScript Request:**
```javascript
const fetch = require('node-fetch');
const BASE_URL = 'http://localhost:3001/api/2024-01/merchant'; // Replace with actual base URL
const accessToken = 'YOUR_ACCESS_TOKEN'; // Replace with your actual access token
const shipmentNumber = '774012744635'; // Replace with the actual shipment number to be deleted

async function deleteShipment() {
  try {
    const response = await fetch(`${BASE_URL}/shipments/${shipmentNumber}`, {
      method: 'DELETE',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${accessToken}`
      },
    });

    if (!response.ok) {
      const errorResponse = await response.json();
      throw new Error(`HTTP error! status: ${response.status}, message: ${errorResponse.errors[0].message}`);
    }
    console.log('Shipment deleted successfully');
  } catch (error) {
    console.error('Fetching error:', error);
  }
}

deleteShipment();
```

**Response:**

The response will confirm the success or failure of the deletion request.

-**When Successful:**
The request will complete without errors, and the response body will typically be empty.

-**When Failed:**
If the shipment cannot be deleted (e.g., due to invalid shipment number, lack of permissions, or other issues), the response will include details about the failure in ${response.status} and ${errorResponse.errors[0].message}


## Get Tracking
**Endpoint:**
```
GET /trackings/{shipmentNumber}
```

**Headers:**
```json
{
  "Content-Type": "application/json",
  "Authorization": "Bearer YOUR_ACCESS_TOKEN"
}
```
Replace YOUR_ACCESS_TOKEN with the actual access token obtained from the authentication process.

**URL Parameters:**
shipmentNumber: The unique identifier of the shipment for which tracking information is being requested.

**Sample JavaScript Request:**
```javascript
const fetch = require('node-fetch');
const BASE_URL = 'http://localhost:3001/api/2024-01/merchant'; // Replace with actual base URL
const accessToken = 'YOUR_ACCESS_TOKEN'; // Replace with your actual access token
const shipmentNumber = '774013244625'; // Replace with the actual shipment number to get tracking info

async function getTracking() {
    try {
        const response = await fetch(`${BASE_URL}/trackings/${shipmentNumber}`, {
            method: 'GET',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${accessToken}`,
            }
        });

        if (!response.ok) {
            const errorResponse = await response.json();
            throw new Error(`HTTP error! status: ${response.status}, message: ${errorResponse.errors[0].message}`);
        }

        const responseData = await response.json();
        console.log('Response Data:', responseData);
    } catch (error) {
        console.error('Fetching error:', error);
    }
}

getTracking();
```

**Response:**
The response will provide detailed tracking information about the shipment.


-**When Successful:**
```json
{
  "number": "774013244625",
  "isDelivered": 1,
  "images": [
    {
      "type": "delivered",
      "url": "https://flashbird.s3.us-west-2.amazonaws.com/driver/cd926d9b-635a-4977-93cb-ff5783264fc01705867432397-65a77d7532c2d0051d0a4ba6.jpeg"
    },
    {
      "type": "signature",
      "url": "https://flashbird.s3.us-west-2.amazonaws.com/driver/96f824b3-4eae-4e9d-8ed9-4951ac1db8011705867440867-65a77d7532c2d0051d0a4ba6.jpeg"
    }
  ],
  "logs": [
    { "message": "Shipment created", "time": 1704152668668 },
    { "message": "Picked up by FlashBird", "time": 1704153477466 },
    { "message": "Out for delivery", "time": 1705475445880 },
    { "message": "Parcel delivered", "time": 1705867447106 }
  ]
}
```

-**When Failed:**
If the tracking information cannot be retrieved (e.g., due to invalid shipment number, lack of permissions, or other issues), the response will include details about the failure.





