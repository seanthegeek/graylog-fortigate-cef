# Fortigate CEF Logs - Graylog Content Pack

This [Graylog][graylog] content pack includes a steam and dashboards for Fortinet Fortigate Common Event Format (CEF) logs.

## Streams

### Fortigate CEF Logs

Routes CEF logs from Fortigates to the `Fortigate CEF Logs` Graylog index set

## Dashboards

### Fortigate - Applications and Devices

Analysis of devices and application traffic

Includes IP addresses, MAC addresses, device manufacturers, and application layer network traffic

### Fortigate - DNS Traffic

Details of DNS queries and responses

Includes details of the query, response, action, and category

### Fortigate - IPS Alerts

Intrusion Prevention System (IPS) alert details

Includes signature, action, severity, source, and destination information

### Fortigate - Overview

An overview of incoming messages from Fortigates

Includes Fortigate hostnames, serial numbers, and full message details

### Fortigate - SSL/TLS Interventions

SSL/TLS actions taken by Fortigates

Provides records of when Fortigates intervened (with or without decrypting) in SSL/TLS traffic

### Fortigate - Web Traffic

Web traffic details

Includes category, action, and more

## Searches

### Fortigate CEF

All Fortigate CEF logs

## Graylog Setup

Edit the Graylog server configuration file at `/etc/graylog/server/server.conf`. Locate the `allow_leading_wildcard_searches` and `allow_highlighting` options, and set both to `true`. Restart the Graylog server by running `sudo systemctl restart graylog-server.service`.

Import the Content Pack into Graylog by navigating to System> Content Packs, clicking on the upload button, and uploading the Content Pack JSON file.

In Graylog an Input accepts log traffic from a source an parses it. That data is sent to Streams, which filters and routes log traffic to Index Sets. Index Sets manage the Elasticsearch indexes that Graylog uses as a backend.

The Stream that comes with this content pack is configured to route the logs to a separate Index Set called `Fortigate CEF Logs`. It does not create the Index Set, so the Index Set needs to be created.

Navigate to System> Indices, and create a new Index Set with a title of `Fortigate CEF Logs` and an index prefix of `fortigate_cef`. Then, click on Streams in the main navigation bar. Edit the `Fortigate CEF Logs` Stream and ensure it is configured to use the Index Set that you just created.

**Important**: Leave `Remove matches from ‘All messages’ stream` box checked, or the data will be duplicated over two Index Sets.

Create a CEF UDP **or** a CEF TCP input by navigating to System> Inputs as a Graylog administrator, and clicking on Launch New Input.

Before creating a CEF TCP input:

Ensure that your certificate and and key are readable by the user running Graylog, or Graylog will create it's own self-signed certificate (which Fortigates will not trust) without informing you in the web UI (this error can be found in `server.log`.)

It is recommended to use a commercial external Certificate Authority (CA). Documentation contributions for using internal CAs would be appreciated. Documentation for using Let's Encrypt Certificates is in progress.

When creating a CEF TLS Input, be sure to check the `Accept encrypted connections` checkbox.

## Fortigate setup

Configure your Fortigates to send data to Graylog in CEF format by using the FortiOS [Command Line Interface (CLI)][CLI].

Replace the server address and port with the address and port of your input, of course.

## Time zone

To simplify and unify log management, it is important that every firewall be configured to use the GMT timezone, which for logging purposes is equivalent UTC.

```fortios
config system global
    set timezone 80
end
```

## Log filtering

By default, logs sent to the syslog server are not filtered. To ensure that the Graylog Input gets all logs, ensure all log filter options are at their default settings.

```fortios
config log syslogd filter
    unset severity
    unset forward-traffic
    unset local-traffic
    unset multicast-traffic
    unset sniffer-traffic
    unset anomaly
    unset voip
end
```

### CEF UDP

**Warning** : UDP traffic is unencrypted.

```fortios
config log syslogd setting
    set status enable
    set server "graylog.example.com"
    set port 5555
    set format cef
    set mode udp
end
```

### CEF TCP

**Warning**: When using CEF TCP, the 'server' setting **must** be set the Graylog server's fully-qualified hostname, **not** the IP address.

```fortios
config log syslogd setting
    set status enable
    set server "graylog.example.com"
    set port 5555
    set format cef
    set mode reliable
end
```

[Graylog]: https://www.graylog.org/
[CLI]: https://docs.fortinet.com/document/fortigate/7.0.1/cli-reference/445620/config-log-syslogd-setting
