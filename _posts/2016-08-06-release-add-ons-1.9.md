---
title:  "Release: Add-Ons 1.9"
---

We are proud to announce the availability of Add-On Administration 1.9, Add-On Self Administration 1.9,
and Add-On Self Administration Plug-In API 1.6.
These releases are mainly fixing bugs.
But there is one new feature in the Add-On Administration.
This release officially deprecates the support for OSIAM 2.x, which will be removed in version 1.12 or 2.0.
This will probably be the last release of all add-ons, as they will be merged into the core server soon.

The artifacts have been deployed on Bintray and in case of the Add-On Self Administration API on the Maven central.
The coordinates of the plug-in API are `org.osiam:addon-self-administration-plugin-api:1.6:jar`.
The files are available using one of the following links:

[Add-On Administration 1.9](https://dl.bintray.com/osiam/downloads/addon-administration/1.9/)

[Add-On Self Administration 1.9](https://dl.bintray.com/osiam/downloads/addon-self-administration/1.9/)

[Add-On Self Administration Plug-In API 1.6](https://dl.bintray.com/osiam/OSIAM/org/osiam/addon-self-administration-plugin-api/1.6/)

The following is an overview of the most important changes.
The full changelogs are available here:

* [Add-On Administration](https://github.com/osiam/addon-administration/blob/1.9/CHANGELOG.md)
* [Add-On Self Administration](https://github.com/osiam/addon-self-administration/blob/1.9/CHANGELOG.md)
* [Add-On Self Administration Plug-In API](https://github.com/osiam/addon-self-administration-plugin-api/blob/1.6/CHANGELOG.md)

## Landing Page for Administration

The Add-On Administration now welcomes its users with a simple landing page.
This solves the issue, that unauthenticated users are immediately redirected to the login page.
It also stops the confusing behavior, when a user logs out and is immediately redirected to the login page again.

## Miscellaneous

- The connector has been updated and support for OSIAM 3.0 has been improved.
- The registration confirmation email now contains the right user id in the activation link instead of a literal "null".
- Changing the display name of a group in the Add-On Administration now retains the group's members.
