FROM bitnami/fluentd:1.10.4
RUN gem install fluent-plugin-elasticsearch --version 1.9.5
RUN gem install fluent-plugin-grok-parser --version 2.1.4

COPY fluent.conf /opt/bitnami/fluentd/conf/custom-fluent.conf

ENV FLUENTD_CONF=custom-fluent.conf