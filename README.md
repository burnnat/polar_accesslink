# Polar AccessLink API Client

[![image](https://img.shields.io/pypi/v/polar_accesslink.svg)](https://pypi.python.org/pypi/polar_accesslink)

Library to enable access to Polar training data through the [Polar Open AccessLink](https://www.polar.com/accesslink-api) API. This library is a forked and packaged version of the offical [Polar AccessLink example client](https://github.com/polarofficial/accesslink-example-python).

  - Free software: MIT license

## Prerequisites

* [Polar Flow](https://flow.polar.com) account

## Getting Started

#### 1. Create new API client 
 
Navigate to https://admin.polaraccesslink.com. Log in with your Polar Flow account and create a new client using an appropriate OAuth2 callback URL for your application. Note the client ID and client secret -- you will need these later.

#### 2. Link user 

User account needs to be linked to client application before client can get any user data. User is asked for authorization in Polar Flow, and user is redirected back to application callback url with authorization code once user has accepted the request. Navigate to 'https://flow.polar.com/oauth2/authorization?response_type=code&client_id=CLIENT_ID' to link your user account. Your application should handle the callback request appropriately, storing the user ID and access token which will be necessary for later API calls. The user must first be registered with the given access token before additional API calls can be made.

Sample code:
```
from accesslink import AccessLink

accesslink = AccessLink(client_id=CLIENT_ID,
                        client_secret=CLIENT_SECRET,
                        redirect_url=REDIRECT_URL)

authorization_code = request.args.get("code")
token_response = accesslink.get_access_token(authorization_code)

USER_ID = token_response["x_user_id"]
ACCESS_TOKEN = token_response["access_token"]

try:
    accesslink.users.register(access_token=ACCESS_TOKEN)
except requests.exceptions.HTTPError as err:
    # Error 409 Conflict means that the user has already been registered for this client.
    # For most applications, that error can be ignored.
    if err.response.status_code != 409:
        raise err
```

#### 3. Access API data

Once user has linked their user account to client application and synchronizes data from Polar device to Polar Flow, application is able to load data.

Sample code:
```
from accesslink import AccessLink

accesslink = AccessLink(client_id=CLIENT_ID,
                        client_secret=CLIENT_SECRET)

user_info = accesslink.users.get_information(user_id=USER_ID,
                                             access_token=ACCESS_TOKEN)
```