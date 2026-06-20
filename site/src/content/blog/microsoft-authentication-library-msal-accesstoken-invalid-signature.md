---
title: "Microsoft Authentication Library (MSAL) – accessToken Invalid Signature"
slug: microsoft-authentication-library-msal-accesstoken-invalid-signature
publishedAt: 2024-04-29
brief: "A fix for the MSAL accessToken invalid signature error, caused by missing default scope — and some frustration with Microsoft's OAuth implementation."
tags: ["microsoft", "msal", "authentication", "nodejs", "development"]
---

I recently had to integrate a Node.js application with Microsoft 365 for authentication purposes. The obvious approach was using the [MSAL Node.js library](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html).

The library is somewhat tedious to use and introduces its own conventions, with numerous seemingly arbitrary hoops to jump through, but eventually it seemed to be working.

I became aware that sometimes users were getting logged out and struggling to log back in. After some digging, I noticed that after an unsuccessful token validation (due to expiry), calling [acquireTokenSilent](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_node.ClientApplication.html#acquireTokenSilent) was returning the _same_ token from the cache. After some further digging, I realised I was incorrectly validating the idToken instead of the accessToken.

It's frustrating that there are no controls in place to prevent the tokens being used interchangeably. It's frustrating that multiple tokens are exposed when I only need one of them.

It's frustrating that Microsoft choose to do their own thing instead of either a) encapsulating the whole process or b) providing a workflow that stays true to OAuth 2.0 (e.g. exposing the refresh token flow).

Switching to the accessToken led to the most annoying problem: the token verification failed due to having an invalid signature. This one had me baffled for a while until some searching on [Stack Overflow](https://stackoverflow.com/a/67001816) led me to realise that I'm not the only mug to fall for this.

Despite being a Microsoft token, and despite verifying the signature with Microsoft keys, it fails — because, if you do not need to pass any scopes as part of your request, you still need to pass a default scope:

```
YOUR_CLIENT_ID/.default
```

So, if your client id is `1111-aaaa`, then your scopes would be:

```
scopes = ['1111-aaaa/.default']
```

Once this was added, the access token signature could be verified and the problems disappeared.

Again, it's frustrating that the error had nothing to do with the problem. It's frustrating that the documentation is not helpful, but in my limited experience this is true of any point where Microsoft technology interfaces with non-Microsoft technology.
