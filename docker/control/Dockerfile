FROM jgoerzen/debian-base-standard:bookworm

ENV container=docker
STOPSIGNAL SIGRTMIN+3

ENV LEIN_ROOT true

# JDK21 only in Debian testing
RUN echo "deb http://deb.debian.org/debian testing main" >> /etc/apt/sources.list
ADD ./apt-preferences /etc/apt/preferences

#
# Jepsen dependencies
#
RUN apt-get -qy update && \
    apt-get -qy install \
        curl dos2unix emacs git gnuplot graphviz htop iputils-ping libjna-java openjdk-21-jdk-headless pssh screen vim wget

RUN wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein && \
    mv lein /usr/bin && \
    chmod +x /usr/bin/lein && \
    lein self-install

# without --dev flag up.sh copies jepsen to these subfolders
# with --dev flag they are empty until mounted
COPY jepsen/jepsen /jepsen/jepsen/
RUN if [ -f /jepsen/jepsen/project.clj ]; then cd /jepsen/jepsen && lein install; fi
COPY jepsen /jepsen/

ADD ./bashrc /root/.bashrc
ADD ./init.sh /init.sh
RUN dos2unix /init.sh /root/.bashrc \
    && chmod +x /init.sh

CMD /init.sh
