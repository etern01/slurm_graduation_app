FROM bitnami/ruby:2.7.8

ENV LANG=en_us.UTF-8
ENV LC_ALL=C.UTF-8
ENV RACK_ENV=production

RUN apt-get update \
&& apt-get install libpq-dev zlib1g-dev -y \
&& rm -rf /var/lib/apt/lists/*

RUN gem install sinatra \
&& gem install redis

RUN gem install pg \
&& gem install aws-sdk-dynamodb pg

COPY yelb-appserver.rb yelb-appserver.rb 
COPY Gemfile Gemfile
COPY modules modules



CMD ["ruby", "/app/yelb-appserver.rb", "-o", "0.0.0.0"]