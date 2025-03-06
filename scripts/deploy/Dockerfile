FROM node:20-slim AS builder

# --max-old-space-size
ENV NODE_OPTIONS=--max-old-space-size=8192
ENV TZ=Asia/Shanghai

# Install pnpm directly instead of using corepack
RUN npm install -g pnpm@9.12.0

WORKDIR /app

# copy package.json and pnpm-lock.yaml to workspace
COPY . /app

RUN pnpm install
RUN pnpm run build

# List the build output directory structure for debugging
RUN find /app/dist -type d | sort

RUN echo "Builder Success "

FROM nginx:stable-alpine as production

RUN echo "types { application/javascript js mjs; }" > /etc/nginx/conf.d/mjs.conf
COPY --from=builder /app/dist/app/apps/web-antd /usr/share/nginx/html

COPY --from=builder /app/scripts/deploy/nginx.conf /etc/nginx/nginx.conf

EXPOSE 8080

# start nginx
CMD ["nginx", "-g", "daemon off;"]
