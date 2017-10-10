FROM alpine
ENV APP_HOME /app
RUN mkdir $APP_HOME
WORKDIR $APP_HOME
ADD Gemfile* $APP_HOME/
ADD . $APP_HOME
RUN apk update && apk add --no-cache ruby ruby-dev build-base && gem install bundler --no-ri --no-rdoc && bundle install && apk del ruby-dev build-base
ENV POST_SERVICE_HOST post
ENV POST_SERVICE_PORT 5000
ENV COMMENT_SERVICE_HOST comment
ENV COMMENT_SERVICE_PORT 9292
CMD ["puma"]
