# Chartlab

## Overview

Chartlab is a publicly hosted NodeJS application that makes it easy to experiment with the visualization library implemented in Axibase Time Series Database.

The Chartlab is hosted at the following URLs:

* http://apps.axibase.com/chartlab
* https://apps.axibase.com/chartlab

Chartlab's primary role is to provide a simple user interface to view and save portal and widget examples. Each example consists of one or multiple widgets built with the ATSD JavaScript visualization library. The widgets, created with JavaScript, serve as API clients and load series, properties and messages from ATSD Data API endpoints.

## Authentication and Authorization

Chartlab is optimized for simplicity and convenience. It doesn't require visitors to create an account to view or save configuration examples.

## Data Sources

The widgets can be configured to load data from the following sources:

* Random datasource
* ATSD instance, operated by Axibase
* Custom ATSD instance

## Connecting Chartlab to Custom ATSD

> Note: The custom ATSD instance should be publicly accessible on a DNS/IP address.

* Login into Axibase Time Series Database server via SSH.

* Open the `/opt/atsd/atsd/conf/server.properties` file.

  Add the following setting:

  ```properties
  api.anonymous.access.enabled = true
  ```

  Save the file.

* Restart the Database

  ```
  /opt/atsd/bin/atsd-tsd.sh stop
  /opt/atsd/bin/atsd-tsd.sh start
  ```

* Open Chartlab on the plain text protocol to avoid security errors.

  http://apps.axibase.com/chartlab/

  > The error will be raised in the browser if the custom ATSD instance is using a self-signed/untrusted SSL certificate.

* Change `Source` to ATSD in the Chartlab top menu.

* Add `url` property to the `[configuration]` section.

  Specify DNS name or IP address of the target ATSD instance.

  ```
  url = http://144.132.12.4:8088/
  ```

  User credentials are not required since ATSD is now configured for anonymous read-only access via Data and Meta API methods.

* Verify that the data is displayed for the following example for a built-in entity/metric:

  ```
  [configuration]
    offset-right = 50
    height-units = 2
    width-units = 1
    url = http://144.132.12.4:8088/

  [group]

  [widget]
    type = chart
    timespan = 15 minute
    format = bytes

    [series]
      entity = atsd
      metric = jvm_memory_used
  ```
