#Select base image

FROM node:22.15.0-alpine3.21 AS build

#Set enviroment to production
ENV NODE_ENV=production

#Working Directory
WORKDIR /api-node-app

#Copy dependency files
COPY package*.json ./

#Set cache and install production modules only
RUN --mount=type=cache,target=/api-node-app/.npm \
  npm set cache /api-node-app/.npm && \
  npm ci --omit=dev

#Copy source code
COPY ./src ./src

##  (multi-stage build)

FROM node:22.15.0-alpine3.21

ENV NODE_ENV=production

WORKDIR /api-node-app

#Set node user and node group as owner and group of /api-node-app directory, since it contains our source code.
RUN chown -R node:node /api-node-app

#Copy only node modules dependencies from build stage and also make node the owner and group owner.
COPY --from=build --chown=node:node /api-node-app/node_modules /api-node-app/node_modules

#Copy only source code required from build stage and set permission rights for node.
COPY --from=build --chown=node:node /api-node-app/src /api-node-app/src

#We are specifying node here because we want it to be run non-root.
USER node

EXPOSE 3000

CMD [ "node", "src/index.js" ]