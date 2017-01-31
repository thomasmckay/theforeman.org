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

With Katello, container images may be synced from external image registries and hosted on-premise. These images are handled like other content with the ability to group them into content views and promote them through lifecycle environments. This would allow, for example, container images to be managed from initial sync to the 'Library' lifecycle environment, though the 'Development' and 'Test' environments, before finally being promoted to the 'Production' environment.

One of the _docker_ subcommands not implemented by default is _docker search_. This article will describe how to enable _docker search_ to work with _Foreman_ either exclusively or in addition to other registries.

```bash
docker search registry
INDEX         NAME                                                                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
example.com   devel.example.com:5000/examplecorp-rhcc-1_0-rhcc-openshift3_ose-docker-registry   Red Hat Container Catalog / openshift3/ose...   0
docker.io     docker.io/registry                                                                The Docker Registry 2.0 implementation for...   1304      [OK]
redhat.com    registry.access.redhat.com/openshift3/ose-docker-registry                         Supports the V2 Docker Registry API. Inclu...   0
```

### Overview

Pulp's Crane allows a couple of external search engines to be configured to process the _docker search_ calls. In this article the _Solr_ search engine will be used. _Solr_ is both simple to setup for basic usage while also being capable of scaling to enterprise configurations.

The _Solr_ container will be run on a _RHEL Atomic Host_ via _Ansible_ for simplicity.

Finally, a script is provided to pull container repository data from the _Foreman_ API to keep _Solr_ up to date with new content.

### Running and configuring the _Solr_ Container

The instructions for starting a _Solr_ container are very well documented and even the basic instructions will suffice.

From the _Solr_ image instructions, the following commands would start a container and initialize a core.

```bash
docker run --name solrcrane -d -p 8983:8983 -t solr
docker exec -it --user solr solrcrane bin/solr create_core -c solrcrane
```

A key component of setting up the _Solr_ search service is defining the database schema necessary for _docker search_ results. _Crane_ expects several fields to be present in the JSON format query results.

```
allTitle - name of container image
ir_description - description of image, free form
ir_automated - boolean true/false if image created via automated build
ir_official - boolean true/false if officially supported image
ir_stars - integer number of user 'likes'
```

When a query from _docker search_ arrives, _Pulp_ will relay the call to the configured search service and then pass the results back to _docker search_. The configuration is of which search service is used is done in _/etc/crane.conf_.

```yaml
# /etc/crane.conf
...

[solr]
url: http://solr.example.com:8983/solr/solrcrane/select?wt=json&q={0}

...
```

Note: After modifying _/etc/crane.conf_ the _httpd_ service must be restarted.
```bash
systemctl restart httpd.service
```

A _Solr_ search database is called a "core". In this example the core is named "solrcrane".



### References
[Katello](https://theforeman.org/plugins/katello/)

[Pulp](http://pulpproject.org/)

[Crane](http://docs.pulpproject.org/en/2.11/nightly/plugins/crane/index.html)

[Solr Container](https://hub.docker.com/r/library/solr/)

[Ansible Documentation](http://docs.ansible.com/ansible/)
