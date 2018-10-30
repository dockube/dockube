ARG ANSIBLE_VERSION=ubuntu1604py3
FROM ansible/ansible:${ANSIBLE_VERSION}

# ================================================================================================
#  Inspiration: Docker Framework (https://github.com/dockube/dockube)
#               Dwi Fahni Denni <zeroc0d3.team@gmail.com>
# ================================================================================================

MAINTAINER "ZeroC0D3 Team <zeroc0d3.team@gmail.com>"

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update; sync
RUN apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" install \
      software-properties-common
RUN apt-add-repository ppa:ansible/ansible
RUN apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" install \
      git \
      bash \
      curl \
      wget \
      openssh-server \
      openssh-client \
      net-tools \
      vim-tiny \
      nano

# ================================================================================================
#  INSTALL SSH
# ================================================================================================
#-----------------------------------------------------------------------------
# Change 'root' Password
#-----------------------------------------------------------------------------
# RUN echo 'root:'${SSH_ROOT_PASSWORD} | chpasswd
RUN echo 'root:root' | chpasswd; sync

RUN sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config

#-----------------------------------------------------------------------------
# Generate Public Key
#-----------------------------------------------------------------------------
# Create new public key
RUN /usr/bin/ssh-keygen -t rsa -b 4096 -C "k8s@tokopedia.local" -f $HOME/.ssh/id_rsa -q -N ""; sync

RUN mkdir -p $HOME/.ssh \
    && touch $HOME/.ssh/authorized_keys \
    && chmod 700 $HOME/.ssh \
    && chmod go-w $HOME $HOME/.ssh \
    && chmod 600 $HOME/.ssh/authorized_keys \
    && chown `whoami` $HOME/.ssh/authorized_keys \
    && cat $HOME/.ssh/id_rsa.pub > $HOME/.ssh/authorized_keys

# Create new pem file from public key
RUN /usr/bin/ssh-keygen -f $HOME/.ssh/id_rsa.pub -e -m pem > $HOME/.ssh/id_rsa.pem

# Create new public key for host
RUN /usr/bin/ssh-keygen -A; sync

COPY ./ssh-key/id_rsa.pub /tmp/ownkey.pub
RUN cat /tmp/ownkey.pub >> $HOME/.ssh/authorized_keys

CMD ["/usr/sbin/sshd", "-D"]

VOLUME /sys/fs/cgroup /run/lock /run /tmp

RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py \
    && python get-pip.py \
    && pip install coverage junit-xml

ENV container=docker

CMD ["/sbin/init"]

EXPOSE 3000
