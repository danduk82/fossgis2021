# Installation

## prerequisites

these examples have been tested on Ubuntu linux 18.04. You also need at least:

- docker (for the test database)
- java runtime version >= 11

## Install baremaps

```bash
# download the latest version
#wget https://github.com/baremaps/baremaps/releases/latest/download/baremaps.zip

# use a fixed version so the examples are still working in the future
wget https://github.com/baremaps/baremaps/releases/download/v0.5.3/baremaps.zip
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
  --name baremaps-db-test \
  --publish 5432:5432 \
  -e POSTGRES_DB=baremaps \
  -e POSTGRES_USER=baremaps \
  -e POSTGRES_PASSWORD=baremaps \
  -d baremaps/postgis:latest
```
