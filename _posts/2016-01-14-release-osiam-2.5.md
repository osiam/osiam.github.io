---
title:  "Release: OSIAM 2.5"
---

We are proud to announce the availability of OSIAM 2.5.
This release mostly contains changes to improve the SCIM 2.0 compliance, internal enhancements and minor fixes.
But there are also some new configuration properties for fine tuning the performance of your OSIAM installation.

We've also decided to remove the add-ons from the release cycle of OSIAM.
This means, that the main version of OSIAM only contains the auth-server and resource-server from now on.
To manifest this, we skipped version 2.4 in auth-server and resource-server.
This synchronizes the version of the core servers with the main version.
These version will always be the same from now on.
It also means that we will not provide the main distribution nor the Debian packages anymore.
There was [some discussion](https://github.com/osiam/osiam/issues/93) about this, and we decided to discontinue these artifact.
If you used the main distribution, then you can easily switch to the single distributions.
There is currently no replacement for the Debian packages, but we will try to bring them back at some point.
You can still [build the distribution or Debian packages](https://github.com/osiam/distribution) yourself.

You can download OSIAM in different packaging formats using one of the following links:

<table>
  <thead>
    <tr>
      <th>Service</th>
      <th>Download</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>auth-server</td>
      <td>
          <a href="https://github.com/osiam/osiam/releases/download/v2.5/auth-server-2.5-distribution.tar.gz">.tar.gz</a>
          <a href="https://github.com/osiam/osiam/releases/download/v2.5/auth-server-2.5-distribution.zip">.zip</a>
          <a href="https://github.com/osiam/osiam/releases/download/v2.5/auth-server-2.5.war">.war</a>
      </td>
    </tr>
    <tr>
      <td>resource-server</td>
      <td>
          <a href="https://github.com/osiam/osiam/releases/download/v2.5/resource-server-2.5-distribution.tar.gz">.tar.gz</a>
          <a href="https://github.com/osiam/osiam/releases/download/v2.5/resource-server-2.5-distribution.zip">.zip</a>
          <a href="https://github.com/osiam/osiam/releases/download/v2.5/resource-server-2.5.war">.war</a>
      </td>
    </tr>
    <tr>
      <td>Docker image</td>
      <td>
          <a href="https://hub.docker.com/r/osiamorg/osiam/">Docker Hub</a>
      </td>
    </tr>
  </tbody>
</table>

It follows an overview of the most important changes.
See the full changelog at the [OSIAM 2.5 release page on GitHub](https://github.com/osiam/osiam/releases/tag/v2.5).

## SCIM 2.0 Compliance

A major aspect of the changes coming in OSIAM 3.0 is improved SCIM 2.0 compliance.
We have backported most of the changes in OSIAM 3.0 to 2.5.
If you use the Connector4Java you will not notice any of the changes below.

### Schemas

Until 2.5, OSIAM used the old, deprecated schema URNs from draft 08.
From now on OSIAM will only accept and produce resources with the current schema URNs.
You can see the changes in the following table:

|-----------------------+----------------------------------------------------------------+-------------------------------------------------------------|
| Resource              | Old Schema URN                                                 |Current Schema URN                                           |
|-----------------------+----------------------------------------------------------------+-------------------------------------------------------------|
| User                  | urn:scim:schemas:core:2.0:User                                 | urn:ietf:params:scim:schemas:core:2.0:User                  |
| Group                 | urn:scim:schemas:core:2.0:Group                                | urn:ietf:params:scim:schemas:core:2.0:Group                 |
| ListResponse          | urn:scim:schemas:core:2.0:User/urn:scim:schemas:core:2.0:Group | urn:ietf:params:scim:api:messages:2.0:ListResponse          |
| ServiceProviderConfig | urn:scim:schemas:core:2.0:ServiceProviderConfig                | urn:ietf:params:scim:schemas:core:2.0:ServiceProviderConfig |
|-----------------------+----------------------------------------------------------------+-------------------------------------------------------------|

### Error Responses

OSIAM responded with strange HTTP status codes in case of some errors.
These have been changed to the ones specified in SCIM 2.0 if applicable.
Refer to the [full changelog](https://github.com/osiam/osiam/releases/tag/v2.5) for details.

The JSON response in case of an error also was wrong.
It has been corrected to look like this now:

{% highlight json %}
{
  "schemas": ["urn:ietf:params:scim:api:messages:2.0:Error"],
  "detail": "Resource 2819c223-7f76-453a-919d-413861904646 not found",
  "status": "404"
}
{% endhighlight %}

### Type of a Group's Member

The JSON response of a group will now properly contain the `type` field of it's members:

{% highlight json %}
{
      "id": "cef9452e-00a9-4cec-a086-d171374aabef",
      "meta": {
        "created": "2016-01-10T14:09:05.855+01:00",
        "lastModified": "2016-01-10T14:09:05.855+01:00",
        "resourceType": "Group"
      },
      "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:Group"
      ],
      "displayName": "group",
      "members": [
        {
          "value": "cef9452e-00a9-4cec-a086-d171374ffbef",
          "type": "User"
        }
      ]
    }
{% endhighlight %}

As you can see, the `type` field of the only member is set to `User`, because this member is a user.

## JDBC Connection Pooling

OSIAM now contains a JDBC connection pool.
Some users reported performance issues with the single database connection OSIAM used before.
You can also change some settings of this pool using the following configuration properties:

- `org.osiam.resource-server.db.maximum-pool-size`: the size of the pool
- `org.osiam.resource-server.db.connection-timeout-ms`: how long to wait to acquire a connection

There are also more new configuration properties that can be used to tune the performance of OSIAM.
Refer to the [full changelog](https://github.com/osiam/osiam/releases/tag/v2.5) for details.

## Retrieve All OAuth Clients

OSIAM's auth server now allows you to get the list of all registered OAuth clients.
To do so, just issue a `GET` request to the `/Client` endpoint like this:

{% highlight sh %}
curl -H "Accept: application/json" -H "Authorization: Bearer $YOUR_ACCESS_TOKEN" -X GET http://localhost:8080/osiam-auth-server/Client
{% endhighlight %}

This will result in a response similar to the following example:

{% highlight json %}
[
  {
    "id": "example-client",
    "accessTokenValiditySeconds": 28800,
    "refreshTokenValiditySeconds": 86400,
    "redirectUri": "http://localhost:5000/oauth2",
    "scope": [
      "ME",
      "ADMIN"
    ],
    "grants": [
      "refresh_token",
      "password",
      "client_credentials",
      "authorization_code"
    ],
    "implicit": false,
    "validityInSeconds": 28800,
    "client_secret": "secret"
  }
]
{% endhighlight %}

## Spring Boot

OSIAM now uses Spring Boot as the underlying framework.
We also completely migrated to Java config, as it is recommended by Spring Boot.
This led to much deletion of configuration and supportive code.
It also gave us a much saner, more stable, and more secure default configuration.

You can now also use [Spring Boot's configuration properties](https://docs.spring.io/spring-boot/docs/1.2.8.RELEASE/reference/html/common-application-properties.html)
in OSIAM's configuration files.
This is not very well tested, so use it at your own risk.

We are going to build executable `.war` files from now on.
This means you can start OSIAM like this if you want:

{% highlight sh %}
java -cp osiam.war:/etc/osiam org.springframework.boot.loader.WarLauncher
{% endhighlight %}

We rely on the static assets and configuration data to be present on the classpath.
Therefore it is not yet possible to simply start OSIAM with `java -jar osiam.war`.
This feature is still experimental, mostly because we don't automatically test OSIAM like this.
But most of the developers are already using this feature for local development and some tests.

## Improved Database Schema

Many fields names and some table names changed in this release, because we switched to Hibernate's `ImprovedNamingStrategy`.
We also got rid of most hard-coded table and field names in the JPA annotations.
There is an automatic database migration, that will run when you update OSIAM.
**Make a backup of your database before you do so!**

Note, that you have to change some of your SQL scripts that install OAuth clients or SCIM extensions.
Refer to the [full changelog](https://github.com/osiam/osiam/releases/tag/v2.5) for details.
