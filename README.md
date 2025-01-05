<p align="center">
<img src="https://github.com/teifun2/cs-unifi-bouncer/raw/main/docs/assets/crowdsec_unifi_logo.png" alt="CrowdSec" title="CrowdSec" width="300" height="280" />
</p>

# CrowdSec Unifi Bouncer
A CrowdSec Bouncer for Unifi appliance

![GitHub](https://img.shields.io/github/license/teifun2/cs-unifi-bouncer)
![GitHub go.mod Go version](https://img.shields.io/github/go-mod/go-version/teifun2/cs-unifi-bouncer)
[![Go Report Card](https://goreportcard.com/badge/github.com/teifun2/cs-unifi-bouncer)](https://goreportcard.com/report/github.com/teifun2/cs-unifi-bouncer)
[![Maintainability](https://api.codeclimate.com/v1/badges/0104e64dccffc4b42f52/maintainability)](https://codeclimate.com/github/teifun2/cs-unifi-bouncer/maintainability)
[![ci](https://github.com/teifun2/cs-unifi-bouncer/actions/workflows/container-release.yaml/badge.svg)](https://github.com/teifun2/cs-unifi-bouncer/actions/workflows/container-release.yaml)
![GitHub tag (latest SemVer)](https://img.shields.io/github/v/tag/teifun2/cs-unifi-bouncer)
![Docker Image Size (latest semver)](https://img.shields.io/docker/image-size/teifun2/cs-unifi-bouncer)

> [!CAUTION]
> This was only tested with an UDM in a homelab environment. Further testing is needed

> [!NOTE]  
> Due to various quirks of the Unifi API this got more complicated than originally planneed.


# Description
This repository aim to implement a [CrowdSec](https://doc.crowdsec.net/) bouncer for the routers of [Unifi](https://www.ui.com/) to block malicious IP to access your services.
For this it leverages [Unifi API](https://ubntwiki.com/products/software/unifi-controller/api) to populate a dynamic Firewall Address List. Specically the Go Library [go-unifi](https://github.com/paultyng/go-unifi) is used.

# Acknowledgment
This is a Fork of [funkolab/cs-mikrotik-bouncer](https://github.com/funkolab/cs-mikrotik-bouncer) and would not have been possible without this previous work

# Usage
For now, this web service is mainly thought to be used as a container.   
If you need to build from source, you can get some inspiration from the Dockerfile.


## Prerequisites
You should have a Unifi appliance and a CrowdSec instance running.   
The container is available as docker image `ghcr.io/teifun2/cs-unifi-bouncer`. It must have access to CrowdSec and to Unifi.   

Generate a bouncer API key following [CrowdSec documentation](https://doc.crowdsec.net/docs/cscli/cscli_bouncers_add)

## Procedure
1. Get a bouncer API key from your CrowdSec with command `cscli bouncers add unifi-bouncer`
2. Copy the API key printed. You **_WON'T_** be able the get it again.
3. Paste this API key as the value for bouncer environment variable `CROWDSEC_BOUNCER_API_KEY`, instead of "MyApiKey"
4. Start bouncer with `docker-compose up bouncer` in the `example` directory
5. It will directly communicate with your Unifi appliance and configure Rules and IP Groups


## Configuration
The bouncer configuration is made via environment variables:

| Name                          | Description                                                                                                        | Default                 | Required |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------|-------------------------|:--------:|
| `CROWDSEC_BOUNCER_API_KEY`    | CrowdSec bouncer API key required to be authorized to request local API                                            | `none`                  |    ✅   |
| `CROWDSEC_URL`                | Host and port of CrowdSec agent                                                                                    | `http://crowdsec:8080/` |    ✅   |
| `CROWDSEC_ORIGINS`            | Space separated list of CrowdSec origins to filter from LAPI (EG: "crowdsec cscli")                                | `none`                  |    ❌   |
| `CROWDSEC_UPDATE_INTERVAL`    | Interval Frequency Querying the Crowdsec API for changes to the blocklist.                                         | `5s`                    |    ❌   |
| `LOG_LEVEL`                   | Minimum log level for bouncer in [zerolog levels](https://pkg.go.dev/github.com/rs/zerolog#readme-leveled-logging) | `1`                     |    ❌   |
| `UNIFI_HOST`                  | Unifi appliance address                                                                                            | `none`                  |    ✅   |
| `UNIFI_USER`                  | Unifi appliance username                                                                                           | `none`                  |    ✅   |
| `UNIFI_PASS`                  | Unifi appliance password                                                                                           | `none`                  |    ✅   |
| `UNIFI_IPV6`                  | Enable / Disable IPv6 support                                                                                      | `true`                  |    ❌   |
| `UNIFI_SITE`                  | Unifi Site Configuration in case of multiple sites                                                                 | `default`               |    ❌   |
| `UNIFI_MAX_GROUP_SIZE`        | UDM has a max IP Group size of 10'000 This might be different for other appliances                                 | `10000`                 |    ❌   |
| `UNIFI_IPV4_START_RULE_INDEX` | If you have other custom Rules defined in your Firewall this might need to be changed to prevent collisions        | `20000`                 |    ❌   |
| `UNIFI_IPV6_START_RULE_INDEX` | If you have other custom Rules defined in your Firewall this might need to be changed to prevent collisions        | `25000`                 |    ❌   |
| `UNIFI_SKIP_TLS_VERIFY`       | Skips Certificate check for unifi controllers without proper SSL Certificate                                       | `false`                 |    ❌   |

# Contribution
Any constructive feedback is welcome, fill free to add an issue or a pull request. I will review it and integrate it to the code.
