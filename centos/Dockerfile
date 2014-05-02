# authorized_keys ファイルを事前に用意して下さい。
FROM centos

# Update to latest version.
RUN yum update -y

# Install core modules.
RUN yum -y install curl
RUN yum -y install ntp
RUN yum -y install unzip
RUN yum -y install wget
RUN yum -y install sudo
RUN yum -y install passwd
RUN yum -y install openssh-server
RUN yum -y install openssh-clients
RUN yum -y clean all

# Time Zone                                             
RUN echo 'ZONE="Asia/Tokyo"' > /etc/sysconfig/clock     
RUN echo 'UTC="false"' >> /etc/sysconfig/clock
RUN rm -f /etc/localtime                                
RUN ln -fs /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
RUN ntpdate ntp.nict.jp                                 

# SSH
RUN /usr/bin/ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key -C '' -N ''
RUN /usr/bin/ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -C '' -N ''

# sudo
RUN echo "%wheel  ALL=(ALL)  ALL" >> /etc/sudoers.d/wheel

# root ユーザーの ssh ログイン禁止
RUN sed -ri 's/^#PermitRootLogin yes/PermitRootLogin no/g' /etc/ssh/sshd_config
# パスワードログイン禁止
RUN sed -ri 's/^UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/^#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config
RUN sed -ri 's/^PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config

# Root User
RUN echo 'root:password' | chpasswd

# General User
RUN useradd developer
RUN gpasswd -a developer wheel
RUN echo 'developer:password' | chpasswd
RUN mkdir -p /home/developer/.ssh
RUN chmod 700 /home/developer/.ssh
ADD ./authorized_keys /home/developer/.ssh/authorized_keys
RUN chmod 600 /home/developer/.ssh/authorized_keys
RUN chown -R developer /home/developer/

EXPOSE 22

