FROM ubuntu:24.04

ARG SSH_PUBKEY

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    openssh-server ca-certificates curl unzip sudo python3 python3-pip && \
    mkdir /var/run/sshd

RUN useradd -m -s /bin/bash ansible && \
    echo "ansible:ansible" | chpasswd && \
    mkdir -p /home/ansible/.ssh && chown -R ansible:ansible /home/ansible/.ssh

RUN usermod -aG sudo ansible
RUN echo 'ansible ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/ansible && chmod 0440 /etc/sudoers.d/ansible

RUN if [ ! -z "$SSH_PUBKEY" ]; then \
      echo "$SSH_PUBKEY" > /home/ansible/.ssh/authorized_keys && \
      chown ansible:ansible /home/ansible/.ssh/authorized_keys && chmod 600 /home/ansible/.ssh/authorized_keys ; \
    fi

RUN sed -i 's/^#PermitRootLogin.*/PermitRootLogin prohibit-password/' /etc/ssh/sshd_config || true
RUN sed -i 's/^#PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config || true

EXPOSE 22 80

CMD ["/usr/sbin/sshd","-D"]