---
layout: post
title: "OpenShift Origin install docker-engine on CentOS"
categories: [Tech, PaaS]
date: "2017-01-22 11:17:21 +0800"
---

* TOC
{:toc}
OpenShift Origin Installation

# OpenShift Origin install docker-engine

***NOTE: This ONLY works for containerized install method, and does NOT work for default RPM based install method, as openshift origin rpm depedens on docker***

Openshift origin is using docker rather than docker-engine.  
If you want to use docker-engine latest version in openshift, you need some modifications for ansible scripts.

## Mods for ansible to install docker-engine
The ansible script for docker installation is roles/docker/tasks/main.yml. Please make below several changes.

- Get current installed Docker version
    ` command: "{{ repoquery_cmd  }} --installed --qf '%{version}' docker-engine"`

- Get latest available version of Docker
    ```
    command: >
     {{ repoquery_cmd  }} --qf '%{version}' "docker-engine"
    ```

- Install Docker
    ```
    package: name=docker-engine{{ '-' + docker_version if docker_version is defined else ''  }} state=present
    ```

## Manually Add docker-engine yum repository to master(s)/node(s)
```
sudo yum-config-manager  --add-repo  https://docs.docker.com/engine/installation/linux/repo_files/centos/docker.repo
```

See [docker doc](https://docs.docker.com/engine/installation/linux/centos/)

## Specify ansible hosts to use latest docker-engine

```
vi /etc/ansible/hosts
docker_version="1.13.0"
```

Then start your ansible installation.
