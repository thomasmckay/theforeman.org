---
layout: post
title: Katello Containers - Enabling "docker search"
date: 2017-01-31
author: @thomasmckay
tags:
- katello
- containers
- pulp
- crane
- docker
excerpt: |
    Learn how to launch a container running Solr to integrate with Pulp's Crane to enable "docker search" in Foreman with Katello
---

### Overview of container image management in Foreman with Katello

Katello integrates with several services for the access and management of various content types. The management of these repositories of content is handled by Pulp. In addition to RPM, Puppet, file, and OSTree content types, Pulp also handles container images. Crane, a plugin to Pulp, exposes a Docker API to these images.

With Katello, container images may be synced from external image registries and hosted on-premise. These images are handled like other content with the ability to group them into content views and promote them through lifecycle environments. This would allow, for example, container images to be managed from initial sync to the Library lifecycle environment, though the Development and Test environments, before finally being promoted to the Production environment.

One of the _docker_ subcommands not implemented by default is _docker search_. This article will describe how to enable _docker search_ to work with _Foreman_ either exclusively or in addition to other registries.

```bash
docker search registry
INDEX         NAME                                                                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
example.com   devel.example.com:5000/examplecorp-rhcc-1_0-rhcc-openshift3_ose-docker-registry   Red Hat Container Catalog / openshift3/ose...   0
docker.io     docker.io/registry                                                                The Docker Registry 2.0 implementation for...   1304      [OK]
redhat.com    registry.access.redhat.com/openshift3/ose-docker-registry                         Supports the V2 Docker Registry API. Inclu...   0
```


### References
[Katello](https://theforeman.org/plugins/katello/)
[Pulp](http://pulpproject.org/)
[Crane](http://docs.pulpproject.org/en/2.11/nightly/plugins/crane/index.html)
[Solr Container](https://hub.docker.com/r/library/solr/)
[Ansible Documentation](http://docs.ansible.com/ansible/)
