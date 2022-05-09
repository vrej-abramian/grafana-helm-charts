### Global ARGs Variables
# Provide the repository's HTTPS URL, since the SSL is not supported by defult.
ARG MONGO_PLUGIN_GIT_URL='https://github.com/JamesOsgood/mongodb-grafana.git'
ARG MONGO_PLUGIN_GIT_DIR='mongodb-grafana'

# Package names (space seperated).
ARG PACKAGES_TO_INSTALL="git tini"
ARG WORKINGDIRECTORY=/

# Plugin directory needs to be available in Grafanai container's Plugins directory.
# Keep in mind that Grafana directory should be shared and accessible with this Plugin service container in the Grafana Pod.
ARG GRAFANA_SHARED_PLUGINS_PATH='/var/lib/grafana/plugins'
ARG GRAFANNA_UID='472'
ARG GRAFANNA_GID="${GRAFANNA_UID}"
##############################################

FROM node:12.22.12-alpine3.15 AS build
ARG MONGO_PLUGIN_GIT_URL
ARG MONGO_PLUGIN_GIT_DIR
ARG PACKAGES_TO_INSTALL
ARG WORKINGDIRECTORY

WORKDIR ${WORKINGDIRECTORY}
RUN apk add ${PACKAGES_TO_INSTALL}      && \
      rm -rf /var/cache/apk/*           && \
      git clone ${MONGO_PLUGIN_GIT_URL} && \
      cd ${MONGO_PLUGIN_GIT_DIR}        && \
      npm install
############################################

FROM node:12.22.12-alpine3.15 AS to-run
ARG GRAFANA_SHARED_PLUGINS_PATH
ARG WORKINGDIRECTORY
ARG MONGO_PLUGIN_GIT_DIR
ARG GRAFANNA_UID
ARG GRAFANNA_GID

ENV GRAFANA_SHARED_PLUGINS_PATH $GRAFANA_SHARED_PLUGINS_PATH
ENV MONGO_PLUGIN_GIT_DIR        $MONGO_PLUGIN_GIT_DIR

WORKDIR ${WORKINGDIRECTORY}
COPY --from=build --chown=${GRAFANNA_UID}:${GRAFANNA_GID}  ${WORKINGDIRECTORY}/${MONGO_PLUGIN_GIT_DIR}/ ./${MONGO_PLUGIN_GIT_DIR}/
COPY --from=build /sbin/tini /sbin/tini

EXPOSE 3333

ENTRYPOINT ["/sbin/tini", "--"]

CMD if [ -d ${GRAFANA_SHARED_PLUGINS_PATH} ] ; then                       \
      get_dir_uid=$( stat -c %u:%g ${GRAFANA_SHARED_PLUGINS_PATH} )     && \
      cp -rfp ${MONGO_PLUGIN_GIT_DIR}  ${GRAFANA_SHARED_PLUGINS_PATH}/  && \
      chown -R ${get_dir_uid}   ${GRAFANA_SHARED_PLUGINS_PATH}/${MONGO_PLUGIN_GIT_DIR} && \
      cd ${GRAFANA_SHARED_PLUGINS_PATH}/${MONGO_PLUGIN_GIT_DIR}         && \
      npm run server                                                    ;\
    else  \
       echo "[ERR]: ${GRAFANA_SHARED_PLUGINS_PATH} - Directory not found." ;\
       exit 111 ;\
    fi 

