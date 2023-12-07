FROM node:lts AS base
WORKDIR /app

# By copying only the package.json and yarn.lock here, we ensure that the following `-deps` steps are independent of the source code.
# Therefore, the `-deps` steps will be skipped if only the source code changes.
COPY package.json yarn.lock ./

FROM base AS prod-deps
RUN yarn install --production

FROM base AS build-deps
RUN yarn install --production=false

FROM build-deps AS build
COPY . .
RUN yarn run build

# FROM base AS runtime
# COPY --from=prod-deps /app/node_modules ./node_modules
# COPY --from=build /app/dist ./dist

# ENV HOST=0.0.0.0
# ENV PORT=4321
# EXPOSE 4321
# CMD node ./dist/server/entry.mjs

FROM nginx:alpine AS runtime
COPY ./docker/nginx.conf /etc/nginx/nginx.conf
COPY --from=prod-deps /app/node_modules /usr/share/nginx/html/node_modules
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 8080