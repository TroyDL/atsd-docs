# IBM Tivoli Monitoring

## Overview

In order to offload detailed data from the ITM infrastructure with minimal latency you need to enable an ITM Warehouse Proxy Agent (WPA) to dump incoming analytical data into CSV files on the local file system. The dump directory will be continously monitored by an `inotify` script, which will upload new CSV files into ATSD as soon as they are created.

This enables ATSD to serve as a long-term repository of historical data. This data is typically deleted after a few months by ITM in order to minimize the disk space usage in the Tivoli Data Warehouse.

Since statistics from ITM agents will be received by ATSD without any delay, the integration can be used for real-time analytics and peformance dashboards.

![](images/itm_diag.png "Warehouse Proxy Agent diagram")

## Installation steps

### Configure ITM Warehouse Proxy Agent

* Configure WPA to store analytical data received from agents into CSV files on the local file system as described [here](http://www-01.ibm.com/support/knowledgecenter/SSATHD_7.7.0/com.ibm.itm.doc_6.3fp2/adminuse/history_analytics_scenarios.htm "WPA").

* Set the `hd.ini` settings to activate private history streaming:

    * Change to the `/opt/ITM directory` and append the following settings to the `config/hd.ini` file:

        ```ini
        KHD_CSV_OUTPUT_ACTIVATE=Y
        KHD_CSV_OUTPUT=/tmp/itm/csv
        KHD_CSV_OUTPUT_TAGGED_ONLY=Y
        KHD_CSV_ISO_DATE_FORMAT=Y
        KHD_CSV_MAXSIZE=400
        KHD_CSV_EVAL_INTERVAL=60
        ```
    * Restart WareHouse Proxy agent:

        ```sh
        bin/itmcmd stop hd
        bin/itmcmd start hd
        ```

### Configure ITM Agents

* Download situation configuration files for the following products:

    - [Linux OS](csv-configs/agents/lz_situations.xml)
    - [VMware](csv-configs/agents/vm_situations.xml)
    - [WebSphere MQ](csv-configs/agents/mq_situations.xml)

* Copy the configuration file to the `localconfig/${PRODUCT_CODE}/` directory on the agent machine, where `${PRODUCT_CODE}` is the agent product code. You can lookup commonly used product codes [here](http://www-01.ibm.com/support/docview.wss?uid=swg21265222).

    > Agent situation files adhere to the following naming convention: ${PRODUCT_CODE}_situations.xml

* Restart the agent:
    
    ```sh
    bin/itmcmd stop ${PRODUCT_CODE}
    bin/itmcmd start ${PRODUCT_CODE}
    ```

### Download CSV Parsers for UX, VM, and MQ Product Codes

   - [Linux OS](csv-configs/atsd/klz-csv-configs.xml)
   - [VMware](csv-configs/atsd/kvm-csv-configs.xml)
   - [WebSphere MQ](csv-configs/atsd/mq-csv-configs.xml)

### Upload CSV Parsers into ATSD

- Login into the ATSD web interface.
- Open the `Configuration->CSV:Parsers` page.
- Click the [Import] button.
- Upload CSV parser xml files that you downloaded previously.

### Configure `inotify` Script to Read CSV files and Upload Them into ATSD

* Download [inotify_sender](inotify_sender.sh) script to your WPA server.

* Specify the ATSD hostname by editing the following line:

    ```sh
    if [ "$url" = "" ]; then
        url="http://atsd_host:8088"
    fi
    ```
	
	
* Set username and password in the `inotify_sender.sh` script

```bash
--user=[[USER]] --password=[[PASSWORD]]
```

* Set permissions to execute the script:
    ```sh
    chmod a+x inotify_sender.sh
    ```

* Run the script with the following command:
    ```sh
    ./inotify_sender.sh
    ```
* Review script logs in the `/tmp/itm/logs` directory.

* Add script to auto-start. The auto-start configuration is dependent on your operating system.

## Verifying Data in ATSD

* Login into ATSD.
* Click on Metrics tab and filter metrics by following prefixes:

 - `klz`
  ![](images/klz_metrics.png)

 - `lnx`
  ![](images/lnx_metrics.png)

 - `kvm`
  ![](images/kvm_metrics.png)


 - `mq`
  ![](images/mq_metrics.png)


## Viewing Data in ATSD

### Metrics

* List of collected [ITM metrics](metric-list.md)

### Entity Groups

- `ITM - Linux OS`

### Portals
- [ITM – Linux OS Portal](http://apps.axibase.com/chartlab/43f054ee)

![](images/itm_linux_portal.png "itm_linux_portal")
