FROM ruby:2.7 as webpage
RUN git clone https://github.com/Mikroways/mikroways.net.git /tmp/webpage
WORKDIR /tmp/webpage
RUN bundle
RUN bundle exec jekyll build

FROM nginx
COPY --from=webpage /tmp/webpage/_site /usr/share/nginx/html