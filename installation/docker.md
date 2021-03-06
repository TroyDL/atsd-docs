# Installation on Docker

## Host Requirements

* [Docker Engine](https://docs.docker.com/engine/installation/) 1.7+.

## Images

### Docker Hub Image Information

* Image name: `axibase/atsd:latest`
* Base: ubuntu:14.04
* [Dockerfile](https://github.com/axibase/dockers/blob/master/atsd/Dockerfile)
* [Docker Hub](https://hub.docker.com/r/axibase/atsd/)

### RedHat Enterprise Linux Activated Image Information

* Image name: `axibase/atsd:rhel7`
* Base: rhel7:latest
* [Dockerfile](https://github.com/axibase/dockers/blob/atsd-rhel7/Dockerfile)
* [readme](https://github.com/axibase/dockers/blob/atsd-rhel7/README.md)

## Start Container

It is often convenient to initialize the database with a shared [collector account](../administration/collector-account.md) with write-only permissions for clients and tools such as agents, scripts, storage drivers that will be inserting data into the database.

Choose one of the options below for starting the ATSD container.

### Option 1: Configure Collector Account Automatically

Replace `clr-user` and `clr-password` in the command below. Minimum password length is **six** (6) characters and the password is subject to the following [requirements](../administration/user-authentication.md#password-requirements).

If the user name or password contains a `$`, `&`, `#`, or `!` character, escape it with backslash `\`.

```properties
docker run \
  --detach \
  --name=atsd \
  --restart=always \
  --publish 8088:8088 \
  --publish 8443:8443 \
  --publish 8081:8081 \
  --publish 8082:8082/udp \
  --env COLLECTOR_USER_NAME=clr-user \
  --env COLLECTOR_USER_PASSWORD=clr-password \
  axibase/atsd:latest
```

### Option 2: Configure Collector Account Manually

Start the database without built-in accounts and configure both administrator and [collector accounts](../administration/collector-account.md) on initial login.

```properties
docker run \
  --detach \
  --name=atsd \
  --restart=always \
  --publish 8088:8088 \
  --publish 8443:8443 \
  --publish 8081:8081 \
  --publish 8082:8082/udp \
  axibase/atsd:latest
```

## Start Container

Execute the command as described above.

```sh
axibase@nurswghbs002 ~]# docker run \
>   --detach \
>   --name=atsd \
>   --restart=always \
>   --publish 8088:8088 \
>   --publish 8443:8443 \
>   --publish 8081:8081 \
>   --publish 8082:8082/udp \
>   --env COLLECTOR_USER_NAME=data-agent \
>   --env COLLECTOR_USER_PASSWORD=Pwd78_ \
>   axibase/atsd:latest
Unable to find image 'axibase/atsd:latest' locally
latest: Pulling from axibase/atsd
bf5d46315322: Pull complete
9f13e0ac480c: Pull complete
e8988b5b3097: Pull complete
40af181810e7: Pull complete
e6f7c7e5c03e: Pull complete
ca48528e7708: Pull complete
de225e971cf6: Pull complete
6a3419ba188d: Pull complete
Digest: sha256:f2c2957b1ffc8dbb24501495e98981899d2b018961a7742ff6adfd4f1e176429
Status: Downloaded newer image for axibase/atsd:latest
14d1f27bf0c139027b5f69009c0c5007d35be92d61b16071dc142fbc75acb36a
```

It may take up to 5 minutes to initialize the database.

## Check Installation

```
docker logs -f atsd
```

You should see an _ATSD start completed_ message at the end of the `start.log` file.


```
...
 * [ATSD] Starting ATSD ...
 * [ATSD] ATSD not running.
 * [ATSD] ATSD java version "1.7.0_111"
 * [ATSD] Waiting for ATSD to start. Checking ATSD web-interface port 8088 ...
 * [ATSD] Waiting for ATSD to bind to port 8088 ...( 1 of 20 )
...
 * [ATSD] Waiting for ATSD to bind to port 8088 ...( 11 of 20 )
 * [ATSD] ATSD web interface:
...
 * [ATSD] http://172.17.0.2:8088
 * [ATSD] https://172.17.0.2:8443
 * [ATSD] ATSD start completed.
```

ATSD web interface is accessible on port 8088/http and 8443/https.

## Launch Parameters

| **Name** | **Required** | **Description** |
|:---|:---|:---|
|`--detach` | Yes | Run container in background and print container id. |
|`--hostname` | No | Assign hostname to the container. |
|`--name` | No | Assign a unique name to the container. |
|`--restart` | No | Auto-restart policy. _Not supported in all Docker Engine versions._ |
|`--publish` | No | Publish a container's port to the host. |
|`--env COLLECTOR_USER_NAME` | No | User name for a data collector account. |
|`--env COLLECTOR_USER_PASSWORD` | No | Password for a data collector account, subject to [requirements](../administration/user-authentication.md#password-requirements).|
|`--env COLLECTOR_USER_TYPE` | No | User group for a data collector account, default value is `writer`.|

## Exposed Ports

* 8088 – http
* 8443 – https
* 8081 – [TCP network commands](/api/network#network-api)
* 8082 – [UDP network commands](/api/network#udp-datagrams)

## Port Mappings

Depending on your Docker host network configuration, you may need to change port mappings in case some of the published ports are already taken.

```sh
Cannot start container <container_id>: failed to create endpoint atsd on network bridge:
Bind for 0.0.0.0:8088 failed: port is already allocated
```

```properties
docker run \
  --detach \
  --name=atsd \
  --restart=always \
  --publish 9088:8088 \
  --publish 9443:8443 \
  --publish 9081:8081 \
  --publish 9082:8082/udp \
  axibase/atsd:latest
```

## Troubleshooting

* Review [Troubleshooting Guide](troubleshooting.md).
* Kernel Incompatibility

Verify that your Docker host runs on a supported kernel level if the container fails to start or the installation script stalls.

```
uname -a
```

* 3.13.0-79.123+
* 3.19.0-51.57+
* 4.2.0-30.35+

See "Workarounds" in [#18180](https://github.com/docker/docker/issues/18180#issuecomment-193708192)

## Validation

* [Verify database installation](verifying-installation.md).

## Post-installation Steps

* [Basic configuration](post-installation.md).
* [Getting Started guide](/tutorials/getting-started.md).
