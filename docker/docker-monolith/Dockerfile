FROM ubuntu:18.04

RUN apt-get update  \
    && apt-get install -y mongodb-server ruby-full ruby-dev build-essential git \
    && apt-get clean cache
RUN gem install bundler -v 2.3.27
RUN git clone -b monolith https://github.com/express42/reddit.git

COPY mongod.conf /etc/mongod.conf
COPY db_config /reddit/db_config
COPY start.sh /start.sh

WORKDIR /reddit

RUN rm Gemfile.lock && bundle install
RUN chmod 0777 /start.sh

CMD ["/start.sh"]