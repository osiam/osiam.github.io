---
title:  "Release: OSIAM 3.0"
---

We are proud to announce the availability of OSIAM 3.0.

TODO: Write more top level stuff

You can download OSIAM using the following link:
[osiam-3.0.war](https://github.com/osiam/osiam/releases/download/3.0/osiam-3.0.war)

It follows an overview of the most important changes.
See the full changelog at the [OSIAM 3.0 release page on GitHub](https://github.com/osiam/osiam/releases/tag/3.0).

## SCIM 2.0 Compliance

TODO: Write me

### Removal of PATCH

TODO: Write me

### `/Me` Endpoint

TODO: Write me

### Misc

TODO: Write me

- Reply with 400 BAD REQUEST to invalid filters.
- Change URL of service provider configuration resource from
  `/ServiceProviderConfigs` to `/ServiceProviderConfig`.
- Always return the `id` attribute, when searching for `User`s.
- Allow colons (:) as field separators for URNs of extensions, since this is
  what the SCIM specification defines. Using periods (.) is still possible,
  but will log a warning message.
- Fields of the core schemas for user and group can be fully qualified, i.e.
  `filter=urn:ietf:params:scim:schemas:core:2.0:User:userName sw "J"`.

## Security

TODO: Write me

### BCrypt Password Hashing

TODO: Write me

### Disable Password Searches

TODO: Write me

### Removal of Method-Based Scopes

TODO: Write me

## Deployment and Configuration

TODO: Write me

### YAML configuration

TODO: Write me

### Merge of the Servers

TODO: Write me

### Running Standalone

TODO: Write me

### Home Directory

TODO: Write me

### Command Line Interface

TODO: Write me

### Configure Extensions

TODO: Write me
