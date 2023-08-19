# Copyright (C) Jacob Salmela, 2020 <me@jacobsalmela.com>
# This file is part of sampo
FROM              bash:4.4
LABEL             maintainer="Jacob Salmela <<me@jacobsalmela.com>"
LABEL             org.opencontainers.image.source = "https://github.com/pknw1/sampo-host-integration"
LABEL             app=${APP}
ARG               SAMPO_PORT=1042
ARG               APP=sampo
# file                  for returning Content-Type and Content-Lenght headers
# netcat-openbsd        for in-container debugging and/or an simpler alternative to socat
# openssh-client        for remote connections using the host's .ssh/ folder when volume mounted
# socat                 the main command used to serve the API
RUN               apk add --no-cache \
                    file \
                    netcat-openbsd \
                    openssh-client \
                    socat \
                    docker
ENV               APP=${APP} \
                  DEBUG=false \
                  SAMPO_PORT=${SAMPO_PORT} \
                  SSH_AUTH_SOCK=/ssh-agent
# Install sampo, the config, and the scripts/ directory
COPY              ./${APP}/sampo.sh /${APP}/sampo.sh
COPY              ./${APP}/sampo.conf /config/sampo.conf
COPY              ./${APP}/scripts /${APP}/scripts
RUN               chmod 0755 /${APP}/${APP}.sh
RUN               chmod 0644 /config/${APP}.conf

# Run the sampo.sh script, and listen on the exposed port from above using socat
# netcat is also included in case you want that (and is also useful for port checking between containers/pods)
# You can also run sampo directly in bash using a loop:
#    while true; do { echo -e 'HTTP/1.1 200 OK\r\n'; bash ./sampo.sh; } | nc -l 1042; done
#

#         TCP-LISTEN:PORT:    socat will listen locally on the specified port
#         reuseaddr,fork      When accepting a connection, socat will fork to handle it. This means that it can handle parallel connections, and won't die after the first connection
#
VOLUME            [ "/ssh-agent"]
EXPOSE            ${SAMPO_PORT}
CMD               socat -d TCP-LISTEN:1042,reuseaddr,fork,pf=ip4 \
                    exec:/${APP}/${APP}.sh
