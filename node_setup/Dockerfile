FROM ubuntu:22.04

# Optimize mirrors for AWS ap-south-1 region speed up apt
RUN sed -i 's|http://archive.ubuntu.com/ubuntu/|http://ap-south-1.ec2.archive.ubuntu.com/ubuntu/|g' /etc/apt/sources.list

# Install SSH, Python, and Sudo
RUN apt-get update && apt-get install -y --no-install-recommends \
    openssh-server \
    python3 \
    sudo \
    && rm -rf /var/lib/apt/lists/*

# 1. Create 'ansible_user' and grant passwordless sudo
RUN useradd -m -s /bin/bash ansible_user && echo "ansible_user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# 2. Hardening: Disable Root Login and Password Auth
RUN mkdir -p /run/sshd && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config && sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

# 3. Set up SSH directory and copy the key
RUN mkdir -p /home/ansible_user/.ssh && chmod 700 /home/ansible_user/.ssh

# Copy SSH public key
COPY id_rsa.pub /home/ansible_user/.ssh/authorized_keys

# 4. Fix ownership and permissions
RUN chown -R ansible_user:ansible_user /home/ansible_user/.ssh && chmod 600 /home/ansible_user/.ssh/authorized_keys

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
