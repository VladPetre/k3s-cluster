# Cassandra cluster

Setup 3 nodes cassandra cluster 
- create ./config folder with jmx-exporter, Dockerfile and metrics config
- docker build -t <image_name> ./config - build an image with jmx exporter integrated
- docker-compose up -d

### !! login with default credentials