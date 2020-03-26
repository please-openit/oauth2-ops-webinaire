# https://docs.docker.com/engine/examples/running_ssh_service/
FROM ubuntu:16.04

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo 'root:THEPASSWORDYOUCREATED' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

# https://github.com/shimt/pam-exec-oauth2
RUN sed -i '1iauth sufficient pam_exec.so expose_authtok /opt/pam-exec-oauth2/pam-exec-oauth2\n' /etc/pam.d/common-auth
RUN printf '%s' "auth sufficient pam_exec.so expose_authtok /opt/pam-exec-oauth2/pam-exec-oauth2" | cat - /etc/pam.d/common-auth
COPY ./pam-exec-oauth2-master/pam-exec-oauth2.linux-amd64 /opt/pam-exec-oauth2/pam-exec-oauth2
COPY ./pam-exec-oauth2.yaml /opt/pam-exec-oauth2/pam-exec-oauth2.yaml

RUN useradd user1; mkdir /home/user1; chown user1:user1 /home/user1

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]