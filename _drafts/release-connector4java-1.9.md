---
title:  "Release: Connector4Java 1.9"
---

We are proud to announce the availability of Connector4Java 1.9.
This release merges the scim-schema into the connector.
This was necessary, because client and server partly have a different view on the SCIM resources.
This leads to an enormous amount of deprecations in this release.
This release deprecates the support of OSIAM 2.x.
It will be completely removed in version 1.12 or 2.0.
Please update to OSIAM 3.0 as soon as possible.

The artifacts have been deployed to Maven central.
We recommend consuming them with Maven, Gradle or any other build tool supporting the Maven repositories.
You can also download the JAR files using one of the following links:

[connector4java-1.9.jar](https://github.com/osiam/connector4java/releases/download/1.9/connector4java-1.9.jar)
([Artifact Details](http://search.maven.org/#artifactdetails%7Corg.osiam%7Cconnector4java%7C1.9%7Cjar))
([GPG Signature](https://github.com/osiam/connector4java/releases/download/1.9/connector4java-1.9.jar.asc))

The following is an overview of the most important changes.
See the full changelog at the [Connector4Java 1.9 release page on GitHub](https://github.com/osiam/connector4java/releases/tag/1.9).

## Updating Resources has been Deprecated

OSIAM 3.0 removes the ability to update resources via PATCH.
This is because the implementation was not compliant with the final SCIM specification.
As users are encouraged to update to OSIAM 3.0, updating resources has been deprecated.
It will be completely removed in 1.12 or 2.0.
Please update your application to use resource replacing via PUT.

## Get Current User

OSIAM 3.0 receives a new `/Me` endpoint, that is compliant to the current SCIM spec.
To use the new endpoint a new method, `getMe()`, has been introduced.
To be backwards compatible, this method works with OSIAM 2.x and OSIAM 3.x.
The methods `getCurrentUser` and `getCurrentUserBasic`, and the class `BasicUser` have been deprecated.
They will be completely removed in 1.12 or 2.0.
Please update your applications to use `getMe` instead of the aforementioned methods.

## Filtering Resource Attributes

OSIAM 3.0 makes it possible to filter the attributes of the returned resources.
To enable this feature in the connector the methods `getUser()`, `getAllUsers()`, `getMe()`, `getGroup()` and `getAllGroups()` now take the attributes requested as optional final String varargs in their parameter list.

Retrieving a list of all `User` resources with only their userName and their creation date can be realized as follows:
    
```java
    List<User> users = osiamConnector.getAllUsers(accessToken, "userName", "meta.created");
```

## SCIM Changes

The `Group.Builder` API has been extended with some methods to change the list of members:

- `addMembers(Collection<MemberRef>)`
- `removeMember(MemberRef)`
- `removeMembers()`

This makes the API consistent with `User.Builder`.

Many methods and classes, that are not needed on the client-side have been deprecated.
OSIAM ignores them anyway, so client applications probably never used them.
Nevertheless, they have only been deprecated, not deleted, to maintain backwards compatibility.

Additional new features in the SCIM API are:

- `Address` has a `display` property now
- The user name can now be set via the builder
- The group's display name can now be set via the builder
- Properties of a user's name attribute can be easily updated now.
