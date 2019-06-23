ARG NODE_VERSION=8
FROM node:${NODE_VERSION}

RUN apt-get update
RUN apt-get install -y linux-headers-amd64 libudev-dev libstdc++6
#RUN git clone --single-branch -b master --depth=1 https://github.com/OpenZWave/open-zwave.git /tmp/open-zwave && \
RUN git clone https://github.com/OpenZWave/open-zwave.git /tmp/open-zwave && \
	cd /tmp/open-zwave && \
	git checkout 996373e6d0003c618fcbe21e467c3a6ca6ad89a3 && \
	ln -s /tmp/open-zwave /tmp/open-zwave-read-only && \
	make && \
	make \
	instlibdir=usr/lib \
	pkgconfigdir="usr/lib/pkgconfig/" \
	PREFIX=/usr \
	sysconfdir=etc/openzwave \
	install && \
	rm -rf /tmp/open-zwave

# Home directory for Node-RED application source code.
RUN mkdir -p /usr/src/node-red

# User data directory, contains flows, config and nodes.
RUN mkdir /data

WORKDIR /usr/src/node-red

# Add node-red user so we aren't running as root.
RUN useradd --home-dir /usr/src/node-red --no-create-home node-red \
	&& chown -R node-red:node-red /data \
	&& chown -R node-red:node-red /usr/src/node-red

USER node-red

# package.json contains Node-RED NPM module and node dependencies
COPY package.json /usr/src/node-red/
RUN npm install

# User configuration directory volume
EXPOSE 1880

# Environment variable holding file path for flows configuration
ENV FLOWS=flows.json
ENV NODE_PATH=/usr/src/node-red/node_modules:/data/node_modules

CMD ["npm", "start", "--", "--userDir", "/data"]
