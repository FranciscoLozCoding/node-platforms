
# This Dockerimage simulates a waggle node without k3s or the higher-level edge code

# docker build -t waggle/wes-minimal .

# mount ansible files into container on start:
# docker run -ti --rm -v ${PWD}/ansible/:/ansible:ro -w /ansible waggle/wes-minimal

# This container will be used in the beekeeper docker-compose enviornment for testing purposes.

FROM ubuntu:18.04

RUN apt-get update && apt-get install -y python3-pip netcat git    # TODO move git to ansible
#RUN python3 -m pip install ansible==4.0.0 # could not install anymore
RUN apt install -y software-properties-common && add-apt-repository --yes --update ppa:ansible/ansible-4 && apt install -y ansible

# do not copy "private" folder
COPY ansible/*.yaml ansible/*.yml ansible/05-sage  /ansible/
COPY entrypoint.sh .

WORKDIR /ansible
ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

ARG VSN=TEST-minimal
ARG NODE_ID=0000000000000001

RUN mkdir -p /etc/waggle && echo $VSN > /etc/waggle/vsn

RUN ansible-playbook -i localhost, -c local ./waggle_os.yml \
    -e skip_registration_key=yes \
    -e nodeid_service_masked=no \
    -e use_systemd=yes \
    -e install_docker=no \
    -e install_k3s=no

RUN ansible-playbook -i localhost, -c local ./waggle_config.yml \
    -e beekeeper_registration_host=bk-sshd \
    -e beekeeper_registration_port=2201 \
    -e node_id=$NODE_ID \
    -e check_k3s=no \
    -e copy_k8s_resource_files=no
    #-o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no

CMD /entrypoint.sh
#CMD /bin/sleep infinity
