FROM ubuntu:latest

# install dependencies needed to build the node addon
RUN apt-get update --quiet \
 && apt-get install --quiet --yes curl make g++ python3 \
 && curl --fail --silent --show-error --location https://deb.nodesource.com/setup_22.x | bash - \
 && apt-get install --quiet --yes nodejs \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 && npm install --global typescript

# copy the test code
COPY calculate_version.sh /work/calculate_version.sh
COPY package.json /work/package.json
COPY test.ts /work/test.ts

# install the skipruntime binary release
RUN VERSION=$(/work/calculate_version.sh | sed 's/^-/v/') \
 && curl --fail --silent --show-error --location https://raw.githubusercontent.com/jberdine/skip/refs/tags/${VERSION}/bin/install_runtime.sh \
  | SOURCE=https://github.com/jberdine/skip/releases/download bash -

# install the current skipruntime packages
COPY skipruntime-native.tgz /work/skipruntime-native.tgz
RUN npm install /work/skipruntime-native.tgz

# build and run the test code
WORKDIR /work
RUN npm install
CMD ["bash", "-c", "tsc --module node16 test.ts && LD_LIBRARY_PATH=/usr/local/lib node test.js"]

# CMD ["bash"]
