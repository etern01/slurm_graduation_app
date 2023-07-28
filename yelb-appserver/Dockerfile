FROM ruby:2.6.10-slim-buster

################## BEGIN INSTALLATION ######################

# Set the working directory to /app
WORKDIR /app

 
COPY Gemfile Gemfile


ENV LANG=en_us.UTF-8
ENV LC_ALL=C.UTF-8
ENV RACK_ENV=production

RUN gem install sinatra --no-document &&  \ 
    gem install redis --no-document
### hack to allow the setup of the pg gem (which would fail otherwise)
RUN apt update && apt install -y \
        build-essential libpq-dev  \
     && rm -rf /var/lib/apt/lists/*

### end of hack (this would require additional research and optimization)
RUN gem install pg --   --with-pg-lib=/usr/lib  && \ 
    gem install aws-sdk-dynamodb pg --no-document
# Set the working directory to /
COPY yelb-appserver.rb yelb-appserver.rb
COPY Gemfile Gemfile
COPY modules modules

WORKDIR /
ADD startup.sh startup.sh

##################### INSTALLATION END #####################

CMD ["./startup.sh"]


