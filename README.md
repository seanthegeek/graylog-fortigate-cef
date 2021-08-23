# Fortigate CEF Logs - Content Pack

This [Graylog][graylog] content pack includes a steam and dashboards for Fortinet Fortigate Common Event Format (CEF) logs. It has been tested with Graylog 4.1.3 and FortiOS 7.0.1, but it should work with FortiOS 6.4 as well.

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

Create a CEF UDP **or** a CEF TCP input by navigating to System> Inputs as a Graylog administrator, and clicking on Launch New input.

Before creating a CEF TCP input:

Ensure that your certificate and and key are readable by the user running Graylog, or Graylog will create it's own self-signed certificate (which Fortigates will not trust) without informing you in the web UI (this error can be found in `server.log`.)

It is recommended to use a commercial external Certificate Authority (CA). Documentation contributions for using internal CAs would be appreciated. Documentation for using Let's Encrypt Certificates is in progress.

When creating a CEF TLS Input, be sure to check the `Accept encrypted connections` checkbox.

## Fortigate setup

Configure your Fortigates to send data to Graylog in CEF format by using the FortiOS [Command Line Interface (CLI)][CLI]. Replace the server address and port with the address and port of your input, of course.

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
