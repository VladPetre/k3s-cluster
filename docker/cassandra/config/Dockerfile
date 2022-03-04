FROM cassandra:3.11.11

COPY jmx_prometheus_javaagent-0.16.1.jar /prometheus/jmx_prometheus_javaagent-0.16.1.jar
COPY prometheus.yml /prometheus/prometheus.yml

RUN echo 'JVM_OPTS="$JVM_OPTS -javaagent:'/prometheus/jmx_prometheus_javaagent-0.16.1.jar=7070:/prometheus/prometheus.yml'"' \
    | tee -a /etc/cassandra/cassandra-env.sh