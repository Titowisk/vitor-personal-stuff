# Auth

## Authentication
Determines who you are.

## Authorization
Determines what can you access or not.

# JWT (Json Web Tokens)
> JWTs or JSON Web Tokens are a simple and secure way to transfer information. They are built around cryptography where the data is signed upon creating the token and therefore its content can be trusted by all other parties. When these tokens are signed they turn into a JSON Web Signature (JWS).

> JWT is a stateless solution that encapsulates all the authorisation information in a self-contained token that can be verified and trusted, without the need to keep states in the database.

The token consists of three parts that are separated by a dot. Each part is encoded with Base64.

The **header** contains information about the algorithm and public key metadata.
The **payload** which contains all the information we’ve added to it.
The **signature** is used to verify the data

JWT are based on key pairs (private and public). Private keys are used to sign the token, and public keys are used to verify if the token can be trusted.

> JWKS (JSON Web Key Set), is a format to store public keys in JSON format and is part of the JSON Web Key (JWK) open standard RFC 7517. It has gained a lot of popularity with JWT plugins because it’s a great way to pack and transport public keys. 

> When deploying a JWT solution it is recommended to periodically change the private and public key to mitigate risks with the private key getting in the hands of attackers. This is called key rotation. JWKS simplifies this process when you need to rotate the key pairs because this lets you hold both the old public key and the new one which makes it possible for existing tokens to still exist until they expire.

## Avoid Storing Sensitive Information in the Payload
The Risk: The JWT payload is encoded but not encrypted, making it readable by anyone who intercepts the token. Avoid storing confidential information in the token.

Solution: If you must transmit sensitive information, consider using JSON Web Encryption (JWE) for full encryption.

# Identity Provider (IdP) vs Service Provider (SP)
- Identity Provider is the auth server
- Service Provider  is the resource server 

In order for the service provider to use a identity provider with SSO, it has to implement the same protocol (SAML, OIDC, WS-Fed)

# SSO (Single Sign On)
> Single Sign-on (SSO) occurs when a user logs in to one application and is then signed in to other applications automatically, regardless of the platform, technology, or domain the user is using. The user signs in only one time, hence the name of the feature (Single Sign-on).

## SAML
> a protocol for implementing SSO

### SAML Assertion
Is a XML file that is used between SP and IdP to convey information about user identity and authorization status

it may contain
- name, email
- roles, permissions
- issuance time, expiry time

# OAuth 
> The OAuth 2.0 **authorization framework** enables a third-party 
   application to obtain limited access to an HTTP service, either on
   behalf of a resource owner by orchestrating an approval interaction
   between the resource owner and the HTTP service, or by allowing the
   third-party application to obtain access on its own behalf.

It focus on thirdy party authorization to resources

# Passkeys
> Passkeys is developed by the FIDO alliance,  and is an authentication method designed to eliminate the need for passwords, providing a seamless login experience without compromising security. Rather than relying on a string of characters (like a password), passkeys use asymmetric cryptography, aka public-key cryptography  to authenticate users.

> Passkeys are more secure than traditional passwords because they rely on something you have instead of something you know.

# OpenId Connect - OIDC
> OpenID Connect or OIDC is an identity protocol that utilizes the authorization and authentication mechanisms of OAuth 2.0. The OIDC final specification was published on February 26, 2014, and is now widely adopted by many identity providers on the Internet

# Federate
To transfer information, particularly authenticantion and authorization, securely between domains

# References

- https://www.nblocks.dev/blog/authentication/state-of-auth-and-the-evolution-of-authentication
- https://datatracker.ietf.org/doc/html/rfc6749#page-4
- https://auth0.com/docs/authenticate/protocols/oauth
- https://auth0.com/docs/authenticate/single-sign-on 
- https://auth0.com/intro-to-iam/what-is-openid-connect-oidc 




# Random Notes

## OAuth + openId Connect MultTenant thirdy party auth server
- implement identifier-first login prompt
- implement OpenId Connect for SSO
- implement SAML for SSO
- add password support after identifier-first login prompt for existing non-federated accounts
- support account linking and unlinking

## OAuth API Identity Provider
- implement OAuth 2.0 server
- implement consent prompt
- implement consent and token revocation
- implement token introspection for opaque tokens
- implement jwt for stateless authorization

# References
- https://www.youtube.com/watch?v=32TMFI4itqo