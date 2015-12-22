---
title:  "Release: Add-Ons 1.8"
---

We are proud to announce the availability of Add-On Self Administration 1.8 and Add-On Administration 1.8.
This release mostly brings compatibility fixes to make the add-ons work with all versions of OSIAM >= 2.2 again.
Support for OSIAM 3.0 has been added too.
A major bug in Add-On Administration was fixed as well.

You can download the add-ons in different packaging formats using one of the following links:

<table>
  <thead>
    <tr>
      <th>Service</th>
      <th>Download</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>addon-self-administration</td>
      <td>
          <a href="https://github.com/osiam/addon-self-administration/releases/download/v1.8/addon-self-administration-1.8-distribution.tar.gz">.tar.gz</a>
          <a href="https://github.com/osiam/addon-self-administration/releases/download/v1.8/addon-self-administration-1.8-distribution.zip">.zip</a>
          <a href="https://github.com/osiam/addon-self-administration/releases/download/v1.8/addon-self-administration-1.8.war">.war</a>
      </td>
    </tr>
    <tr>
      <td>addon-administration</td>
      <td>
          <a href="https://github.com/osiam/addon-administration/releases/download/v1.8/addon-administration-1.8-distribution.tar.gz">.tar.gz</a>
          <a href="https://github.com/osiam/addon-administration/releases/download/v1.8/addon-administration-1.8-distribution.zip">.zip</a>
          <a href="https://github.com/osiam/addon-administration/releases/download/v1.8/addon-administration-1.8.war">.war</a>
      </td>
    </tr>
  </tbody>
</table>

It follows an overview of the most important changes.
See the full changelogs at the [Add-On Self Administration 1.8 release page on GitHub](https://github.com/osiam/addon-self-administration/releases/tag/v1.8)
and the [Add-On Administration 1.8 release page on GitHub](https://github.com/osiam/addon-administration/releases/tag/v1.8).

## Add-On Self Administration

### Compatibility

In release 1.7 the Add-On Self Administration introduced an incompatibility with versions of OSIAM <= 2.3.
This release restore this compatibility.
To connect to OSIAM <= 2.3, you have to set the configuration property `org.osiam.connector.legacy-schemas` to `true`:

{% highlight properties %}
org.osiam.connector.legacy-schemas=true
{% endhighlight %}

For more information see the [Add-On Self Administration documentation](https://github.com/osiam/addon-self-administration/blob/v1.8/docs/configuration.md#orgosiamconnectorlegacy-schemas).

Also, support for OSIAM 3.0 has been added.
To connect to OSIAM 3.0, you have to use the configuration property `org.osiam.home` instead of the other endpoint properties:

{% highlight properties %}
org.osiam.home=http://localhost:8080/osiam
{% endhighlight %}

For more information see the [Add-On Self Administration documentation](https://github.com/osiam/addon-self-administration/blob/v1.8/docs/configuration.md#osiam-3x).

### Changed Scopes

The Add-On Self Administration now uses the scope `ADMIN` to connect to OSIAM.
With this we are dropping support for the deprecated method-based scopes.
This means you have to update the respective OAuth client before you can update the add-on.
To do this issue the following SQL statement:

{% highlight sql %}
INSERT INTO osiam_client_scopes (id, scope) VALUES (<id of addon-self-administration-client>, 'ADMIN');
{% endhighlight %}

Most of the time `<id of addon-self-administration-client>` will be `10`.
If you have a non-standard setup, then it might have a different value.

### Plug-In API 1.5

A new version of the Add-On Self Administration Plug-In API has been released.
It's built against the latest SCIM schema and we changed the dependency on it to scope `provided`.
See the full changelog and downloads at the
[Add-On Self Administration Plug-In API 1.5 release page on GitHub](https://github.com/osiam/addon-self-administration-plugin-api/releases/tag/v1.5).

## Add-On Administration

### Compatibility

In release 1.7 the Add-On Administration introduced an incompatibility with versions of OSIAM <= 2.3.
This release restore this compatibility.
To connect to OSIAM <= 2.3, you have to set the configuration property `org.osiam.connector.legacy-schemas` to `true`:

{% highlight properties %}
org.osiam.connector.legacy-schemas=true
{% endhighlight %}

For more information see the [Add-On Administration documentation](https://github.com/osiam/addon-administration/blob/v1.8/README.md#osiam-endpoints-osiam-2x).

Also, support for OSIAM 3.0 has been added.
To connect to OSIAM 3.0, you have to use the configuration property `org.osiam.endpoint` instead of the other endpoint properties:

{% highlight properties %}
org.osiam.endpoint=http://localhost:8080/osiam
{% endhighlight %}

For more information see the [Add-On Administration documentation](https://github.com/osiam/addon-administration/blob/v1.8/README.md#osiam-endpoints-osiam-3x).

### Changed Scopes

The Add-On Administration now uses the scope `ADMIN` to connect to OSIAM.
With this we are dropping support for the deprecated method-based scopes.
This means you have to update the respective OAuth client before you can update the add-on.
To do this issue the following SQL statement:

{% highlight sql %}
INSERT INTO osiam_client_scopes (id, scope) VALUES (<id of addon-administration-client>, 'ADMIN');
{% endhighlight %}

Most of the time `<id of addon-administration-client>` will be `20`.
If you have a non-standard setup, then it might have a different value.

### Boolean Types Extension Fields

Due to a bug, all boolean-types extension fields always displayed the value as `true`, regardless of the real value stored.
Furthermore it was not possible to set the fields to anything else than `true`.
This release fixes this behavior so you can safely use boolean-typed extension fields again.
See also: [https://github.com/osiam/addon-administration/issues/83](https://github.com/osiam/addon-administration/issues/83).
