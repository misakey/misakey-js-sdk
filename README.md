# The Misakey SDK

A NodeJS package that lets you send and get text messages and files to users through Misakey.

Sending messages:

```javascript
const fs = require('fs');
const Misakey = require('@misakey/sdk');

const misakey = new Misakey(YOUR_ORG_ID, YOUR_ORG_AUTH_SECRET, YOUR_ORG_CRYPTO_SECRET);
misakey.pushMessages({
  messages: [
    `Hello ${DATA_SUBJECT}, here is your data :-)`,
    {
      filename: PATH_TO_FILE.split('/').pop(-1),
      data: fs.readFileSync(PATH_TO_FILE),
    },
  ],
  boxTitle: `Your ${DATATAG} data`,
  dataSubject: DATA_SUBJECT,
  dataTag: DATATAG,
}).then((boxInfo) => console.log(boxInfo));
```

Output:

```
{
  boxId: 'd8dcbd74-a50e-42fb-a25d-1618f07da4f4',
  datatagId: '35aec043-bb95-4c93-8d1b-e311021baba1',
  invitationLink: 'https://app.misakey.com/boxes/d8dcbd74-a50e-42fb-a25d-1618f07da4f4#h_iSvbUr_3wnNQ6SK6-p6hS2U9xXbuzFYewxJz2jIwY',
  dataSubjectNeedsLink: true
}
```

Reading text and files from all boxes for a given datatag, data subject and data producing org:

```javascript
// note: data subject must given her consent for you to get this data
misakey.getData(DATA_SUBJECT, DATATAG, PRODUCER_ORG_ID).then((data) => console.log(data));
```

Output:

```
{
  boxes: [
    {
      id: '4b2ea2c7-e0d0-427e-baec-6e7244e43de9',
      title: 'Your purchase data',
      creationDate: '2021-05-17T13:45:11.833176Z',
      messages: [
        {
          id: '535b9383-b770-447d-ba2d-3c2fa90d406f',
          date: '2021-05-17T13:45:11.907809Z',
          type: 'file',
          file: {
            name: 'purchase-by-michel.pdf',
            data: Uint8Array(4171) [
               37,  80,  68,  70,  45,  49,  46,  53,  10,  37, 181, 237,
              174, 251,  10,  52,  32,  48,  32, 111,  98, 106,  10,  60,
               60,  32,  47,  76, 101, 110, 103, 116, 104,  32,  53,  32,
               48,  32,  82,  10,  32,  32,  32,  47,  70, 105, 108, 116,
              101, 114,  32,  47,  70, 108,  97, 116, 101,  68, 101,  99,
              111, 100, 101,  10,  62,  62,  10, 115, 116, 114, 101,  97,
              109,  10, 120, 156, 109,  90,  75, 174, 228, 176,  13, 220,
              251,  20, 125, 129, 116, 172, 191, 116, 140,  28,  33, 104,
               32, 201, 226, 189,
              ... 4071 more items
            ]
          }
        },
        {
          id: 'd1f1be5b-705d-44a5-b793-58157d954519',
          date: '2021-05-17T13:45:11.881925Z',
          type: 'text',
          text: "Hello michel@misakey.com, here is your data :-)"
        }
      ]
    },
    {
      id: '14621651-d99f-443f-816f-9ce0d8c868cd',
      title: 'Data for another purchase',
      creationDate: '2021-05-17T13:45:11.743996Z',
      messages: [
        {
          id: '9967f072-9d81-4fab-b685-a243679174d3',
          date: '2021-05-17T13:45:11.807566Z',
          type: 'file',
          file: {
            name: 'yet-another-receipt.pdf',
            data: Uint8Array(4171) [
               37,  80,  68,  70,  45,  49,  46,  53,  10,  37, 181, 237,
              174, 251,  10,  52,  32,  48,  32, 111,  98, 106,  10,  60,
               60,  32,  47,  76, 101, 110, 103, 116, 104,  32,  53,  32,
               48,  32,  82,  10,  32,  32,  32,  47,  70, 105, 108, 116,
              101, 114,  32,  47,  70, 108,  97, 116, 101,  68, 101,  99,
              111, 100, 101,  10,  62,  62,  10, 115, 116, 114, 101,  97,
              109,  10, 120, 156, 109,  90,  75, 174, 228, 176,  13, 220,
              251,  20, 125, 129, 116, 172, 191, 116, 140,  28,  33, 104,
               32, 201, 226, 189,
              ... 4071 more items
            ]
          }
        },
        {
          id: 'dbac8d0e-d71c-41ee-8cba-bd88821408ed',
          date: '2021-05-17T13:45:11.780324Z',
          type: 'text',
          text: 'Thank you for shopping with us so frequently!'
        }
      ]
    }
  ]
}
```

If you want to point to a different base domain that `misakey.com`
(typically, to point to a test/demo domain)
you can do so by setting environment variable `MISAKEY_SDK_BASE_TARGET_DOMAIN`.

Another environment variable you can use is `NODE_DEBUG=misakey-sdk`
which will turn debug logging on.

See also example file `example.js`.
Note that it *requires* `MISAKEY_SDK_BASE_TARGET_DOMAIN` to be set,
so that you don't unintentionally call `misakey.com` while tinkering with the SDK.
It also requires you to create a file `example-variables.js`
that exports the variables it needs:

```javascript
// file: example-variables.js
module.exports = {
  ORG_ID: 'c83c496f-b801-465c-a678-4616fa9fd36f',
  ORG_AUTH_SECRET: 'nDUJ4hIYsbrfFJ0QBkQUWQB8/giwapLHAqJ6IQpgd0Y=',
  DATA_SUBJECT: 'michel@misakey.com',
  BOXES: [
    {
      title: 'Your Contract',
      dataTag: 'contract',
      messages: [
        'Please find below your contract:',
        {
          pathToFile: '/var/data/contracts/michel-at-misakey-dot-com.pdf',
        },
        'Thank you for choosing us.',
      ]
    },
    {
      title: 'Your order',
      dataTag: 'purchase',
      messages: [
        'Hi, please find below your receipt and other related documents',
        {
          pathToFile: '/var/data/purchases/1D6FC90D5/receipt.pdf',
        },
        {
          pathToFile: '/var/data/purchases/1D6FC90D5/infalatable-unicorn-user-manual.pdf',
        },
        'Thanks for shopping with us ☺.',
      ]
    }
  ],
};
```

```
$ MISAKEY_SDK_BASE_TARGET_DOMAIN=misakey.com.local node example.js
{
  boxId: '89defdad-d913-4bfa-8648-41328748188f',
  datatagId: '35aec043-bb95-4c93-8d1b-e311021baba1',
  invitationLink: 'https://app.misakey.com.local/boxes/89defdad-d913-4bfa-8648-41328748188f#j_NzjPZd6iDGE5uXzGlvuMS1EeSkMHYuOMRtFXSFZHU',
  dataSubjectNeedsLink: true
}
```

## Authenticate User (Data Consent)

Misakey SDK provides functions that allow to ask a user the access to their data.
The consent is given though a authenticated consent flow performed with [Ory Hydra](https://www.ory.sh/hydra/docs/).

### Prerequisites
 
You must configure the Hydra client for your organization as decribed in [Misakey Documentation](https://docs.misakey.com/guides/your-org#create-your-organization)

### In your server application

You need to implement an endpoint in your application server that will collect the result of the authenticated consent request and redirect the result on your client application.

To do that, `@misakey/sdk` provides a class `MisakeyServer` that contains the method `exchangeUserToken`.

**MisakeyServer**

- **type**: `class`
- **constructor arguments**: 
    - `clientId`: Hydra client identifier defined in [Prerequisites](#prerequisites)
    - `clientSecret`: Hydra client secret defined in [Prerequisites](#prerequisites)
- **returns** instance of `MisakeyServer`

#### exchangeUserToken

* **type**: `func`
* **arguments**: 
  1. type `object`: object containing query parameters returned by Hydra (`error`, `errorDebug`, `errorHint`, `code`, `state`, `codeVerifier`, `scope`)   
  1. type `object`: Object containing URLs that collect the result of the flow
      - `client`: the url of the client application that process the verification at the end of the flow (Cf. [Finishing an authentication flow](#finishing-an-authentication-flow))
      - `server`: the url of the endpoint that calls this `exchangeUserToken` function
* **returns** `object`:
  - `idToken`: the id_token of the authenticated user
  - `clientCallbackLocation`: the client callback url with query parameters for [Finishing an authentication flow](#finishing-an-authentication-flow)
  - `accessToken`: the access_token of the authenticated user, you can use it as http only cookie to authenticate your user
  - `scope`: the token scope
  - `state`: the state generated by the client application on step [Launch authenticated consent flow with Misakey](#Launch-authenticated-consent-flow-with-Misakey)
  - `expiresIn`: the number of second before the access token expires


**Example**

```js
import Misakey from '@misakey/sdk'; // or import { MisakeyServer as Misakey } from '@misakey/sdk'

const CALLBACK_CONFIG = {
  client: 'http://localhost:3000/callback',
  server: 'http://localhost:5000/user/callback'
}

app.get('/user/callback', (req: Request, res: Response) => {
    try {
        const misakey = new Misakey('<YOUR CLIENT ID>', '<YOUR CLIENT SECRET>');
        const { 
          idToken, 
          clientCallbackLocation, 
          accessToken 
        } = await misakey.exchangeUserToken(req.query, CALLBACK_CONFIG);

        res.setHeader('Set-Cookie', `misakey_access_token=${accessToken}; HttpOnly`);
        
        res.writeHead(302, {Location: clientCallbackLocation});
        res.end();
    } catch (error) {
        try {
          console.error('ERROR:', error);
          res.writeHead(302, {Location: `http://localhost:3000/callback?error=${error.message}`});
        } finally {
          res.status(500);
        }
    }
});

```

### In your client application

#### Launch authenticated consent flow with Misakey

To launch an authenticated consent flow, instanciate `MisakeyClient` in your client side application.

**MisakeyClient**

Do not try to use `MisakeyClient` in your backend, it is designed to be run in browser, it will not compile on node. 

- **type**: `class`
- **constructor arguments**: 
    - `organizationId`: organizationId defined in [Prerequisites](#prerequisites)
    - `redirectUri`: the url of your backend endpoint defined in [exchangeUserToken](#exchangeUserToken)
- **returns** instance of `MisakeyClient`

##### userConsent

* **type**: `func`
* **arguments**: 
  1. type `string`: user identifier. Will be used as `loginHint`
  1. type `array`: Array containing the scopes user should consent to. Will be transformed to `scope`
  1. type `object`: additional arguments (optional)

| name | description | default |
| -- | -- | -- |
| clientId (`string`) |  the identifier of client application registered with the OIDC provider |  the `organizationId` passed to **MisakeyClient** |
| redirectUri (`string`) | The redirect URI of the client application to receive a response from the OIDC provider | the one passed to **MisakeyClient**   |
| responseType (`string`) | The type of response desired from the OIDC provider | `"code"`, no other value is handled for now |
| referrer (`string`) | the URL (pathname) where user will be redirected at the end of the flow | pathname from where auth flow has be asked |
| displayHints (`string`) | Stringify JSON object that contains hints about the querier of auth flow. Handled props: `resourceName`, `client` (object with `name` and `logoUri`), `identifier` | `undefined` |
| acrValues (`number`) | Authentication Context Class Reference: specify the minimum ACR asked for the authentication flow | `null`, backend will compute the ACR according to user identity |
| display<br>maxAge<br>uiLocales<br>idTokenHint<br>resource<br>request<br>requestUri<br>responseMode | [See openid documentation](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) |

* **returns** `void`: redirect to authentication url

**Example**

```js
import { MisakeyClient } from '@misakey/sdk';

const misakeySdk = new MisakeyClient({
  organizationId: '2df5d7ae-90b6-4b01-a055-2caff9a0beab',
  redirectUri: 'http://localhost:5000/user/callback'
});

misakeySdk.userConsent(
  'michel@misakey.com', 
  ['datatag.contract.la_redoute', 'datatag.purchase.la_redoute']
)

```

##### Finishing an authentication flow

On the pathname you defined in [exchangeUserToken](#exchangeUserToken), call `validateUserConsent` method to verify the validity of the response of the consent flow.

* **type**: `func`
* **arguments**: none
* **returns** `object`: 
   - `user` (`expired: bool`, `scope`, `email`,... rest of id_token profile), 
   - `callbackHints`: object containing the referrer, the url from where has been launched the flow [#userConsent](#userConsent)

**Example**

```js
// index.js

import { MisakeyClient } from '@misakey/sdk';

const misakeySdk = new MisakeyClient({
  organizationId: '2df5d7ae-90b6-4b01-a055-2caff9a0beab',
  redirectUri: 'http://localhost:5000/user/callback',
});

if (window.location.pathname === "/callback") {
  const { user, expiresAt, callbackHints } = await misakeySdk.validateUserConsent();
  const { referrer } = callbackHints;
  // store user, redirect to referrer 
}

```
