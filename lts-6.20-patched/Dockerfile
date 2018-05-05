ARG LTS_SLUG=lts-6.20

FROM dplusic/stack-ghcjs:$LTS_SLUG

ARG STACK_GLOBAL_PROJECT=/root/.stack/global-project

# The following RUN command fixes an issue like this:
#
# npm ERR! EXDEV: cross-device link not permitted, rename '/root/.stack/global-project/node_modules/finalhandler' -> '/root/.stack/global-project/node_modules/.finalhandler.DELETE'
#
# Credits due https://github.com/npm/npm/issues/13306#issuecomment-236876133
RUN cd $(npm root -g)/npm \
 && npm install fs-extra \
 && sed -i -e s/graceful-fs/fs-extra/ -e s/fs.rename/fs.move/ ./lib/utils/rename.js

WORKDIR $STACK_GLOBAL_PROJECT

# Install libraries to enable static file serving for GHCJSi
RUN npm install finalhandler serve-static

# Add a custom irunner.js that allows serving static files in GHCJSi session
# (this can be helpful to develop with custom index.html, use CSS and JavaScript scripts, images, etc.)
#
# You can specify directory to serve in GHCJSI_STATIC_DIR environment variable.
ADD irunner.js .
RUN mv irunner.js $(dirname $(stack path --global-pkg-db))/.

WORKDIR /

ENV NODE_PATH=$STACK_GLOBAL_PROJECT/node_modules

EXPOSE 6401

CMD ["stack", "ghci"]
