---
gallery: true
image: https://cldup.com/FvmMZUtEaF.png
categories:
- multifactor
fields:
  - value: DUO_MFA:CLIENT_ID
    description: The client_id of the Auth0 client for which you want to require MFA.
    type: auth0:client
    mandatory: true
  - value: DUO_MFA:DUO_IKEY
    description: Your DUO ikey. Get if from here.
    type: string
    mandatory: true
  - value: DUO_MFA:DUO_SKEY
    description: Your DUO skey. Get if from here.
    type: string
    mandatory: true
  - value: DUO_MFA:DUO_API_HOSTNAME
    description: Your DUO API hostname (e.g. api-XXXXXXXX.duosecurity.com)
    type: string
    mandatory: true
---

## Multifactor with Duo Security

This rule is used to trigger multifactor authentication with [Duo Security](http://duosecurity.com) when a condition is met.

Upon first login, the user can enroll the device.

You need to create two __integrations__ in __Duo Security__: one of type __WebSDK__ and the other __Admin SDK__.

```js
function (user, context, callback) {

  var CLIENTS_WITH_MFA = [ configuration['DUO_MFA:CLIENT_ID'] ];
  // run only for the specified clients
  if (CLIENTS_WITH_MFA.indexOf(context.clientID) !== -1) {
    // uncomment the following if clause in case you want to request a second factor only from user's that have user_metadata.use_mfa === true
    // if (user.user_metadata && user.user_metadata.use_mfa){
      context.multifactor = {
        //required
        provider: 'duo',
        ikey: configuration['DUO_MFA:IKEY'],
        skey: configuration['DUO_MFA:SKEY'],
        host: configuration['DUO_MFA:API_HOSTNAME'],

        // optional. Force DuoSecurity everytime this rule runs. Defaults to false. if accepted by users the cookie lasts for 30 days (this cannot be changed)
        // ignoreCookie: true,

        // optional. Use some attribute of the profile as the username in DuoSecurity. This is also useful if you already have your users enrolled in Duo.
        // username: user.nickname,

        // optional. Admin credentials. If you provide an Admin SDK type of credentials. auth0 will update the realname and email in DuoSecurity.
        // admin: {
        //  ikey: 'DIAN...NV6UM',
        //  skey: 'YL8OVzvoeeh...I1uiYrKoHvuzHnSRj'
        // },
      };
    // }
  }

  callback(null, user, context);
}
```
