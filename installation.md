# Installation

## Install baremaps

```bash
wget https://github.com/baremaps/baremaps/releases/latest/download/baremaps.zip
unzip baremaps.zip && rm baremaps.zip
export PATH=$PATH:`pwd`/baremaps/bin
```

## select java version and use openjdk-11

```bash
sudo update-alternatives --config java
sudo update-alternatives --config javac
sudo update-alternatives --config jar
```

## create DB

```bash
docker run \
  --name baremaps-db \
  --publish 5432:5432 \
  -e POSTGRES_DB=baremaps \
  -e POSTGRES_USER=baremaps \
  -e POSTGRES_PASSWORD=baremaps \
  -d baremaps/postgis:latest
```
