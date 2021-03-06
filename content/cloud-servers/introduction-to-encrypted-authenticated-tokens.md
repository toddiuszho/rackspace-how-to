---
permalink: introduction-to-encrypted-authenticated-tokens/
node_id: 4766
title: Introduction to encrypted authenticated tokens
type: article
created_date: '2015-07-28'
created_by: Constanze Kratel
last_modified_date: '2016-01-21'
last_modified_by: Catherine Richardson
product: Cloud Servers
product_url: cloud-servers
---

Rackspace Cloud Identity supports tokens of
different *types* and *formats* to enable a user to authenticate against
a specific Rackspace Cloud service.

-   The token *type* specifies whether the user is provisioned
    or federated.
-   The token *format* specifies the composition of the token itself.

Rackspace Cloud Identity has switched the authentication token format
from UUID to Authenticated Encryption (AE). Rackspace engineers
have implemented the new token format on the Identity system back
end. The change has minimal impact to Rackspace customers. The main
difference that you will notice is that the authentication token value
returned by the Identity service has a different pattern and length than
the UUID token values issued previously.

**Note:**  Make sure that you follow Best practices for handling authentication tokens (located further down in this article),
especially if you use SDK or CLI tools to interact with the Rackspace
Cloud.

This article explains the two different token formats and provides best
practices for working with authentication tokens in general.

### What is an AE token?

An AE token is generated by using Authenticated Encryption.
Authenticated encryption specifies a way to secure a message so that
others cannot fake it, change it, or read it.

Authenticated encryption is used to generate *non-persistent* tokens for
user authentication. An AE token contains all the necessary data to
determine whether a given token is valid, instead of pointing to this
data. With AE tokens, all the metadata is encrypted within the token
itself. Because AE tokens contain all the relevant data, there is no
need to the store tokens in persistent storage. When the server receives
the token, it can parse the token metadata to determine if it is valid.

Because of encryption, the size of an AE-formatted token varies. The
Rackspace Cloud Identity service limits the size of AE-formatted tokens
to 250 bytes.

The following example shows a token object from the authentication
response with an AE token ID.

    "token": {
          "id": "ABCDEF7RbnU-LLWJ1J8PeHRGMz2Cf3rPUG_a25hQRWTcL7tH231H7ubr6y1EkRi_curq6PqJV-pCiIADZrwFtCexcy9MVO3eckgGWqDqnxvXaUMF7XA_reFwwp3pNu_7p9uXofGmiueccwrA",
          "expires": "2015-08-20T23:51:19.055Z",
           "tenant": {
           "id": "123456",
           "name": "123456"
             }

### What is a UUID token?

Authentication tokens that use the UUID token format
are *persistent* tokens. When a user authenticates successfully, the
Rackspace Cloud Identity service generates a 32 character UUID token
value. It is stored in a persistent storage unit on the back end along
with metadata about that token such as expiration time stamp, who the
token is issued to, and so on. The token value is returned to the user
so that it can be included in subsequent requests to Rackspace Cloud
services to confirm identity.

When the user submits a request with the token, the Identity service
validates the token value against the data that is stored in the
persistent storage to confirm that the user is authorized to perform the
operation. When a UUID token expires, the user must re-authenticate.
Then, the Identity service issues a new token and also deletes the
expired token from the persistent backend storage unit.

The following example shows a token object from the authentication
response with a UUID token id.

    "token": {
          "id": "b726839ca0fd4d9ead8edbb73f123456",
          "expires": "2015-08-20T23:48:50.793Z",
          "tenant": {
          "id": "123456",
          "name": "123456"
             }

### What is the difference between UUID and AE tokens?

UUID and AE tokens differ in their persistence, length, and storage.

-   UUID tokens are *persistent. *AE tokens are *non-persistent*. With a
    UUID token, you receive a token when you authenticate. That token
    persists in back-end storage for 24-hours and the same value is
    returned each time you authenticate until the token expires. With AE
    tokens, the value is non-persistent, which means that the value is
    not stored on the backend, and the Identity service generates and
    returns a new token value each time the user authenticates.
-   UUID tokens are 32 characters in length. AE tokens vary in size but
    for the Rackspace Cloud Identity service they are limited to
    250 bytes. With the implementation of AE tokens, you will notice
    that the token value returned when you authenticate is significantly
    longer than the value returned when the Identity service issued
    UUID tokens.
-   UUID tokens are stored in the Identity service back-end along with
    the metadata for authentication.  AE tokens provide the required
    authentication metadata within the encrypted token value. The
    Identity service does not store the AE token value in the
    back-end system.

### Best practices for handling authentication tokens

Following are some best practices for handling authentication tokens.

-   When you authenticate to the Rackspace Cloud Identity service be
    sure to cache the token value that is returned.

    The Rackspace Cloud Identity service validates the authentication
    token in every API request before attempting to complete
    the operation. To optimize your API operations and reduce system
    load, store the authentication token in a secure cache or database
    so that applications can use the stored value instead of requiring
    the application to issue an authentication request before each
    API operation. You can reuse the cached token value as long as it
    remains valid.

    **Note:** For an example of how to cache credentials with an SDK,
    see [Caching credentials](http://php-opencloud.readthedocs.org/en/latest/caching-creds.html) in
    the php-opencloud documentation.

-   Design applications to reauthenticate after receiving
    a `401 Unauthorized`{.code} response from a service endpoint, or to
    check the token expiration and reauthenticate before the token
    expires.

-   To simplify authentication, credential, and token management, use
    an [OpenStack command-line client application](https://wiki.openstack.org/wiki/OpenStackClients) or
    one of the [Rackspace SDKs](https://developer.rackspace.com/sdks/).

For more information, read the [Manage authentication tokens](https://developer.rackspace.com/docs/cloud-identity/v2/developer-guide/#manage-authentication-tokens) section
in the [Rackspace Cloud Identity Client Developer Guide](https://developer.rackspace.com/docs/cloud-identity/v2/developer-guide/).
