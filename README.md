Initially official docker images from CentOS, Ubuntu, Debian available in hub.docker.com doesn't support systemd, so in the community we've a lot of topics and discussions about how create a docker image with systemd, but actually the goal is not using systemd for Workloads using Docker, so Why are you creating this project?

Basically images available in this project are for using together kitchen.ci. The idea is support tests that involving systemd, for example https://www.inspec.io/docs/reference/resources/service.

Originally many users do it using vagrant and virtualbox.

# Restrictions
To running docker with systemd you've got to be capable to run containers in privilege mode and image require local access to cgroup **(/sys/fs/cgroup)**

## Example using docker cli
```
$ docker run --privileged --name ubuntu-systemd -d -i wmaramos/kitchen-ubuntu:16.04
```
**invoke container bash**
```
$ docker exec -it ubuntu-systemd bash
```

## Example using kitchen-docker and kitchen-ansible

```
$ cat kitchen.yml
---
driver:
  name: docker
  privileged: true

provisioner:
  name: ansible_playbook
  hosts: localhost
  require_ansible_repo: true
  ansible_verbose: true
  ansible_version: latest
  require_chef_for_busser: false
  playbook: test/test.yml
  enable_yum_epel: true

verifier:
  name: inspec
  format: junit
  output: test/reports/%{platform}_%{suite}_inspec.xml

platforms:
  - name: ubuntu-16.04
    driver_config:
      image: wmaramos/kitchen-ubuntu:16.04
      run_command: /sbin/init
  - name: centos-7
    driver_config:
      image: wmaramos/kitchen-centos:7
      run_command: /usr/sbin/init
  - name: debian-8
    driver_config:
      image: wmaramos/kitchen-debian:8
      run_command: /sbin/init

suites:
  - name: default
    verifier:
      inspec_tests:
        - test/integration/default
```

## Credits
* https://hub.docker.com/r/centos/systemd
* https://hub.docker.com/r/solita/ubuntu-systemd
