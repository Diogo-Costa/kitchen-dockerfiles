FROM debian:8

ENV container docker
ENV LC_ALL C
ENV DEBIAN_FRONTEND noninteractive

RUN echo 'deb http://deb.debian.org/debian jessie-backports main' >> /etc/apt/sources.list

RUN apt-get update && \
    apt-get install dbus openssh-server -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN find /etc/systemd/system \
    /lib/systemd/system \
    -path '*.wants/*' \
    -not -name '*journald*' \
    -not -name '*systemd-tmpfiles*' \
    -not -name '*systemd-user-sessions*' \
    -exec rm \{} \;

VOLUME ["/sys/fs/cgroup"]

COPY sshd_config /etc/ssh/sshd_config

RUN systemctl set-default multi-user.target
RUN systemctl enable /lib/systemd/system/ssh.service

STOPSIGNAL SIGRTMIN+3

CMD ["/bin/bash", "-c", "exec /sbin/init --log-target=journal 3>&1"]
