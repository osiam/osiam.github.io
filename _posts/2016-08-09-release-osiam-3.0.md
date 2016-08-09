---
title:  "Release: OSIAM 3.0"
---

We are proud to announce the availability of OSIAM 3.0.
This version increases the SCIM 2.0 compliance, contains security enhancements,
and improves deployment and configuration.
The biggest change is the merge of the auth-server and the resource-server.
But custom and outdated APIs like the Facebook Connect emulation and the PATCH API were also removed.
In terms of security, the most important change is the automatic migration to bcrypt hashes for passwords.

OSIAM is available under the following link:

[osiam-3.0.war](https://dl.bintray.com/osiam/downloads/osiam/3.0/osiam-3.0.war)
([GPG Signature](https://dl.bintray.com/osiam/downloads/osiam/3.0/osiam-3.0.war.asc))

It follows an overview of the most important changes.
See the full changelog of [OSIAM 3.0](https://github.com/osiam/osiam/blob/3.0/CHANGELOG.md) for more information.

## Deployment and Configuration

**NOTE:** This part of OSIAM changed heavily, making it incompatible with any previous version.
For a complete overview of the necessary steps to update, see the [migration notes](https://github.com/osiam/osiam/blob/master/docs/migration.md#from-25-to-30).
It's also recommended to read the complete [installation guide](https://github.com/osiam/osiam/blob/master/docs/detailed-reference-installation.md)
to fully understand the new features and changes.

Until now, OSIAM was distributed as a bunch of distinct services.
There were the auth-server and resource-server, that formed the required core of OSIAM.
And there were the add-ons, that extended OSIAM with an administration, a registration process and much more.
With OSIAM 3.0, this is not the case anymore.
It was decided to make OSIAM a single application again.
More information about this decision can be found in the corresponding [GitHub issue](https://github.com/osiam/osiam/issues/43).
As this is a complex undertaking, the start was made with only the core services, i.e the
auth-server and resource-server.
So from now on, the core functions of OSIAM are distributed as a single package containing only 1 service.

There are also no distribution archives anymore.
The only distributed artifact is the OSIAM `.war` file.
This file already contains everything OSIAM needs, e.g. templates and web assets.
These files will be automatically extracted in a new "home directory", when OSIAM starts.
But it is still possible to extract them manually if required.
The home directory can be placed anywhere, but defaults to `~/.osiam`.
It is the place where everything OSIAM needs at runtime lives now.
This also includes the configuration file, making it a replacement for `/etc/osiam` in a default 2.x installation.

Speaking of configuration file: The supported format for it was changed from a Java properties file to YAML.
This means existing configuration files need to be rewritten into a single YAML file.
Fortunately, it's very easy to do so.
See the [migration notes](https://github.com/osiam/osiam/blob/master/docs/migration.md#create-a-new-configuration-file)
for more details.

Another great feature of OSIAM 3.0 is that a Tomcat server is not needed any more.
While deploying OSIAM to Tomcat is still possible, it can also be run as a standalone application now.
Just start OSIAM with `java -jar osiam.war`.
But that's not all.
The `.war` file is not only an executable Java web archive, it's actually a fully executable file.
If executable permissions are set on it, it can be started like any other application on the command line:

```sh
chmod +x osiam.war
./osiam.war
```

Having this feature made it possible to add a simple command line interface to OSIAM.
With OSIAM 3.0 some basic commands can be executed directly on the command line.
Currently there two commands defined:

Creating and initializing the home directory:

```sh
./osiam.war initHome [--osiam.home=/path/to/home/directory]
```

And creating or updating the database schema:

```sh
./osiam.war migrateDb [--osiam.home=/path/to/home/directory]
```

Finally, custom SCIM 2.0 extensions can now be easily configured in the configuration file.
This is the first feature based on the new YAML format for configuration.
It is not necessary to directly change data in the database any more.
See how it works in the [installation guide](https://github.com/osiam/osiam/blob/master/docs/detailed-reference-installation.md#configuring-scim-extension).

## Security

The custom algorithm for hashing passwords has been replaced with [bcrypt](https://en.wikipedia.org/wiki/Bcrypt).
This is more secure, because bcrypt is mature and widespread hashing algorithm.
It's also future-proof, because you can increase the cost factor, which increases the time to validate a hash.
The default cost factor is currently set to `13`, which is considered fair enough these days.
For now, there is no way to change the cost factor, but this will be provided in a later version of OSIAM.
When a user logs in, their password will be automatically migrated to bcrypt.
New users will, of course, get a bcrypt hashed password right away.
Support for old password hashes will be removed in OSIAM 4.0.

OSIAM 2.x allowed searching for a user by their hashed password.
This was necessary, so that the auth-server could check for valid login requests.
With the merge of the auth-server and resource-server, this feature became obsolete.
It also could be a possible attack vector and is not covered by the SCIM 2.0 specification.
Therefore, we removed this feature.

## SCIM 2.0 Compliance

The old PATCH API has become obsolete long ago and was not compliant with the final SCIM 2.0 specification.
We removed it to make fresh start for implementing the new PATCH API.
In the meantime, resources can be updated via the PUT API.
See the [migration notes](https://github.com/osiam/osiam/blob/master/docs/migration.md#api-changes)
on this topic for more details.

The previous versions of OSIAM provided a custom endpoint under `/me`.
This endpoint returned the user that was associated with the access token.
But it did not return the user as a SCIM 2.0 resource, but as a Facebook Connect formatted user.
This was part of the Facebook Connect service provider emulation, that has been removed in 3.0.
The old endpoint was replaced with a SCIM 2.0 compliant version under `/Me`.
The new endpoint will return the currently authenticated user as a SCIM 2.0 resource.
As before, it will not work with access tokens obtained with the client credentials grant.

There are also some minor changes that increase compliance with the final SCIM 2.0 specification:

- Using an invalid filter when querying resources now replies with 400 BAD REQUEST.
- The URL of the service provider configuration resource changed from `/ServiceProviderConfigs`
  to `/ServiceProviderConfig`.
- The `id` attribute will always be returned, when searching for `User`s and filtering attributes.
- Colons (:) should now be used as separators between URNs and extension fields.
- Fields of the core schemas for user and group can be fully qualified,
  i.e. `filter=urn:ietf:params:scim:schemas:core:2.0:User:userName sw "J"`.
