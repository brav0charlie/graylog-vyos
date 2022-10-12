# graylog-vyos
[Graylog](https://graylog.org) [Content Pack](https://docs.graylog.org/docs/content-packs) for [VyOS](https://vyos.io) Routers

## About
graylog-vyos is a content pack for Graylog that provides configuration to make monitoring VyOS firewall logs easier.

For a quick-start tutorial on getting Graylog set up in Docker, I wrote a blog post on [Sending VyOS Syslog to Graylog](https://blog.billclark.io/), which includes a section on spinning up Graylog in Docker. 

## Features
### Inputs
Syslog UDP Input on Port 514

#### Input Extractors
Input Extractors to extract message fields into individually searchable fields (includes all common firewall log fields)

### Custom Grok Pattern
Custom Grok Pattern: SYSLOG_HEADER_VYOS

### Pipelines & Pipeline Rules
One pipeline configured to identify and process firewall messages
- Changes rule_action from A to Accept, D to Drop, and R to Reject.
- Copies field 'frame_type' to 'ethertype'
- Performs Source Address Geo-Lookup

### GeoIP Lookup
This feature requires a copy of the MaxMind GeoLite2-City.mmdb file. The config expects to find a copy at `/etc/graylog/server/GeoLite2-City.mmdb`. You'll need to sign up for an account on MaxMind to obtain a copy of the database.

⚠️ **IMPORTANT NOTE!** If you choose to skip this feature, you'll need to disable this rule. Head to your System menu, then choose Pipelines. Click the little **edit** button on VyOS Firewall Messages, then click the **Edit** button under the **Stage 1** section. Click the **Remove** button to the right of *GeoIP Lookup: src_ip*. Click Save, and you're good to go.

## Installation Notes
After installing the content pack, if you notice that your pipeline isn't Geocoding source addresses, or it appears that the firewall rule_action contains "R", "D", and "A" instead of Reject, Drop, or Accept, you may need to change your Graylog config a bit.

Head to System -> Configurations, and at the top right look at the Message Processors Configuration list. You'll want to ensure that the Pipeline Processor runs *after* the Message Filter Chain. The message filter chain is responsible for extracting data from the message into fields, and the rule in Stage 0 of the VyOS Firewall Messages pipeline relies on two fields created by the message extractors. 

Click the little blue Update button below the Message Processors Configuration list, and you'l be able to change the order in which they run.