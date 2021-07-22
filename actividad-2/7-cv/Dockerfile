FROM node:16.5 as resume
RUN git clone https://gitlab.com/juanpsm/juanpsm.gitlab.io.git /tmp/resume
WORKDIR /tmp/resume
RUN npm i -q \
&& npm run resume export -- --theme elegant index.html

FROM nginx
COPY --from=resume /tmp/resume/index.html /usr/share/nginx/html