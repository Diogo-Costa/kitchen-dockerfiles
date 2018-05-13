FROM ubuntu:16.04

ENV container docker

# Don't start any optional services except for the few we need.
RUN find /etc/systemd/system \
    /lib/systemd/system \
    -path '*.wants/*' \
    -not -name '*journald*' \
    -not -name '*systemd-tmpfiles*' \
    -not -name '*systemd-user-sessions*' \
    -exec rm \{} \;

RUN apt-get update && \
    apt-get install -y dbus openssh-server && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

VOLUME [ "/sys/fs/cgroup" ]

COPY sshd_config /etc/ssh/sshd_config

RUN systemctl set-default multi-user.target
RUN systemctl enable /lib/systemd/system/ssh.service

STOPSIGNAL SIGRTMIN+3

# Workaround for docker/docker#27202, technique based on comments from docker/docker#9212
CMD ["/bin/bash", "-c", "exec /sbin/init --log-target=journal 3>&1"]
