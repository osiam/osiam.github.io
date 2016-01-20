---
title:  "Release: Connector4Java 1.8 and SCIM&nbsp;Schema&nbsp;1.6"
---

We are proud to announce the availability of Connector4Java 1.8 and SCIM Schema 1.6.
These releases restores the compatibility with versions of OSIAM <= 2.3 and introduces support for OSIAM 3.0.
There are also 2 new features: set network timeouts per connector and management of OAuth 2 clients.

The artifacts have been deployed to Maven central.
We recommend consuming them with Maven, Gradle or any other build tool supporting the Maven repositories.
You can also download the JAR files using one of the following links:

<table>
  <thead>
    <tr>
      <th>Artifact</th>
      <th>Download</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>connector4java</td>
      <td>
          <a href="http://search.maven.org/#artifactdetails%7Corg.osiam%7Cconnector4java%7C1.8%7Cjar">Central</a>
          <a href="https://github.com/osiam/connector4java/releases/download/v1.8/connector4java-1.8.jar">.jar</a>
      </td>
    </tr>
    <tr>
      <td>scim-schema</td>
      <td>
          <a href="http://search.maven.org/#artifactdetails%7Corg.osiam%7Cscim-schema%7C1.6%7Cjar">Central</a>
          <a href="https://github.com/osiam/scim-schema/releases/download/v1.6/scim-schema-1.6.jar">.jar</a>
      </td>
    </tr>
  </tbody>
</table>

It follows an overview of the most important changes.
See the full changelogs at the [Connector4Java 1.8 release page on GitHub](https://github.com/osiam/connector4java/releases/tag/v1.8)
and the [SCIM schema 1.6 release page on GitHub](https://github.com/osiam/scim-schema/releases/tag/v1.6).

## Manage OAuth Clients

You can now manage your OAuth clients via the Connector4Java.
To get a list of all clients use:

{% highlight java %}
import org.osiam.client.oauth.Client;

...

List<Client> clients = connector.getClients(accessToken);
{% endhighlight %}

To get a specific client:

{% highlight java %}
Client client = connector.getClient("example-client", accessToken);
{% endhighlight %}

To create a client:

{% highlight java %}
Client client = new Client.Builder("example-client-2", "secret-2")
        .accessTokenValiditySeconds(6000)
        .refreshTokenValiditySeconds(0)
        .redirectUri("http://localhost:5055/oauth2")
        .scopes(Sets.newHashSet(Scope.ADMIN.getValue()))
        .grants(Sets.newHashSet(GrantType.CLIENT_CREDENTIALS.name(),
                GrantType.AUTHORIZATION_CODE.name()))
        .implicit(false)
        .validityInSeconds(60)
        .build();

Client createdClient = connector.createClient(client, accessToken);
{% endhighlight %}

Note that `createdClient` might not contain the same values as `client`.
The server can override given values and will set defaults for missing values.

To delete a client:

{% highlight java %}
connector.deleteClient("short-living-client", accessToken);
{% endhighlight %}

To change a client:

{% highlight java %}
Client client = new Client.Builder("example-client", "new-secret")
        .accessTokenValiditySeconds(300)
        .refreshTokenValiditySeconds(60000)
        .redirectUri("http://localhost:5055/oauth2")
        .scopes(Sets.newHashSet(Scope.ME.getValue()))
        .grants(Sets.newHashSet(GrantType.REFRESH_TOKEN.name(),
                                GrantType.AUTHORIZATION_CODE.name()))
        .implicit(true)
        .validityInSeconds(120)
        .build();

Client updatedClient = connector.updateClient("example-client", client, accessToken);
{% endhighlight %}

Note that `updatedClient` might not contain the same values as `client`.
The server can override given values and will set defaults for missing values.

## Network Timeouts per Connector

You can now set network timeouts per connector instance:

{% highlight java %}
connector = new OsiamConnector.Builder()
        ...
        .withConnectTimeout(150)
        .withReadTimeout(500)
        ...
        .build();
{% endhighlight %}

You can still globally set timeouts like before.
Use this to set timeouts for all connector instances or global defaults that can be overridden per connector.
For more information see the [Connector4Java documentation](https://github.com/osiam/connector4java/blob/v1.8/docs/create-osiam-connector.md#timeouts).

## Compatibility

In release 1.7 the Connector4Java introduced an incompatibility with versions of OSIAM <= 2.3.
This release restore this compatibility.
To connect to OSIAM <= 2.3, you have to set the configuration option `withLegacySchemas` on the `OsiamConnector` to `true`:

{% highlight java %}
OsiamConnector osiamConnector = new OsiamConnector.Builder()
       ...
       .withLegacySchemas(true)
       ...
       .build();
{% endhighlight %}

For more information see the [Connector4Java documentation](https://github.com/osiam/connector4java/blob/v1.8/docs/create-osiam-connector.md#legacy-schemas).

Also, support for OSIAM 3.0 has been added.
This enables you to test your current applications against OSIAM 3.0, that is still in development, without having to change the dependencies.
To connect to OSIAM 3.0, you have to use the configuration option `withEndpoint` on the `OsiamConnector` instead of the other endpoint methods:

{% highlight java %}
OsiamConnector osiamConnector = new OsiamConnector.Builder()
       ...
       .withEndpoint(OSIAM_ENDPOINT)
       ...
       .build();
{% endhighlight %}

For more information see the [Connector4Java documentation](https://github.com/osiam/connector4java/blob/v1.8/docs/create-osiam-connector.md#osiam-3x).

## Immutable SCIM Classes

We always thought that all SCIM classes were immutable, but taking a closer look revealed that this wasn't the case.
So, to preserve thread safety, we overhauled all SCIM classes to make them truly immutable.
We still don't document this fact, mostly because it is hard to automatically proof.
This means, that you shouldn't rely on immutability when passing objects to untrusted clients.
They might find a way to change the object's data.

## Deprecations

Some classes and methods have been deprecated in this release.
They will be removed in release 1.9 or 2.0 of the SCIM schema.
The deprecated items are:

* `org.osiam.resources.scim.Constants`

    The contained constants have been moved to the respective classes or completely removed.

* `org.osiam.resources.helper.SCIMHelper`

    The only contained method is now part of the `User` class.

* `org.osiam.resources.helper.UserDeserializer#UserDeserializer(Class<?> valueClass)`
