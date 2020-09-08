# Monitoring

## Grafana

### Loki

Loki is a log aggregation tool created by the people behind Grafana, inspired by Prometheus. There are many other tools like it, Graylog or a fully-fledged ELK (Elasticsearch, Logstash or Kibana) stack to name a few. There are multiple reasons why Loki makes more sense for a mediaserver setup than the other alternatives, mainly:

    * Its lighter to run, it does not index the whole line, pretty much just metadata (unless told otherwise)
    * Natively supported in Grafana
    * Relatively easy to configure

But why (collect logs)? Stats are cool 😎, it also aids in detecting problems that may arise. It can know about problems before you do, or assist in debugging existing issues. Paired with system-monitoring you will be able to look at system metrics and logs in the same timeframe.
