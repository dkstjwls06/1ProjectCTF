ARG IMAGE
FROM $IMAGE

ARG APT_OLD
RUN /bin/bash -c \
        'if [ $APT_OLD -eq 1 ]  ; then sed -i -re "s/([a-z]{2}.)?archive.ubuntu.com|security.ubuntu.com/old-releases.ubuntu.com/g" /etc/apt/sources.list ; fi'

ENV DEBIAN_FRONTEND noninteractive
RUN apt update -y > /dev/null
RUN apt upgrade -y > /dev/null
RUN apt install xinetd -y > /dev/null

# custom installation
# RUN dpkg ~
# RUN apt install ~
# RUN pip install ~

ARG USER
ARG TIMEOUT
ARG EXEC
RUN useradd -ms /bin/bash $USER
ADD ./share /home/$USER
RUN echo "#!/bin/bash\n cd /home/$USER;  timeout $TIMEOUT /home/$USER/$EXEC" \
        > /home/$USER/run.sh
RUN chown -R root:$USER /home/$USER
RUN chmod -R 750 /home/$USER

ARG SERVICE
RUN echo "service $SERVICE\n{\n\tdisable = no\n\tflags = REUSE\n\tsocket_type = stream\n\tprotocol = tcp\n\tuser = $USER\n\twait = no\n\tserver = /home/$USER/run.sh\n\ttype = UNLISTED\n\tport = 10001\n}" \
        > /etc/xinetd.d/$SERVICE
CMD ["/usr/sbin/xinetd", "-dontfork"]
