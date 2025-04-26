# go-e Charger Network Configuration



## Device-to-App Connection in Local Network

The go-e Charger App from the [Play Store](https://play.google.com/store/apps/details?id=co.goe.app&hl=de_AT) / [App Store](https://apps.apple.com/at/app/go-e/id1287123220) will automatically find your go-e Device with an mDNS search in the local network.
mDNS requires that multicast traffic is not blocked in your network. The go-e App and go-e Device must be in the same subnet and cannot be separated by any kind of router, firewall or NAT boundaries. Especially some older Wi-Fi repeaters do not bridge the network but instead route it through a NAT and therefore interrupt the connection between App and Device.

## Device-to-Cloud Connection
The charger can connect to a cloud service hosted by go-e. This cloud connection can be disabled, but is essential for following functionality:
- Charging session reports and graphical charts in the app
- Update of flexible energy tariffs
- Load balancing
- OTA Updates

For this to work the device will connect to following services:
- `wss://iot.v3.go-e.io` or `wss://iot.v3.go-e.com` (mandatory) for the WebSocket cloud connection for go-e Chargers
- `wss://iot.controller.go-e.io` or `wss://iot.controller.go-e.com` (mandatory) for the WebSocket cloud connection for go-e Controllers
- `https://update.v3.go-e.io` or `https://update.v3.go-e.com` (optional) for OTA Updates

*Currently we are migrating from `go-e.io` to `go-e.com`.*

For performance reasons, we use DNS-based load balancing for the WebSocket Connection. The Device uses DNS server advertised by your DHCP server or the static DNS server configured in the app, and will use a random IP address of the result list. If the Endpoint is not reachable, the device will reconnect every 10 seconds until it reaches the cloud endpoint.

Keep in mind that the IP Addresses of the WebSocket Cloud Service can change constantly and we highly discourage you from hardcoding the IP address you get from a DNS query in the moment you configure it. If your firewall requires hard-coded IP Addresses, you need to automate the workflow for whitelisting IP Addresses based on the DNS query for the hostnames mentioned above.

The charger uses certificate pinning for the Cloud Service and also presents a client certificate. It is not possible to use the Cloud Connection with a network doing MITM on HTTPS traffic.

#### Offline functionalities
Following functionality can still be accessed in the local network with a disabled cloud connection:
- Local App connection in the same network
- Local API (HTTP, MQTT, Modbus TCP)
- PV surplus charging with the go-e Controller
- All OCPP connections

The charger can optionally use the NTP server `europe.pool.ntp.org` to fetch the time if the cloud connection is not enabled. It is also possible to set the time via an a local API.

## App-to-Cloud Connection

If the device cannot be reached in the local network, the App is still able to connect to your go-e Device over the cloud.
For this to work, the App connects to the following services:

- `wss://app.v3.go-e.io` (mandatory) for the WebSocket connection to speak to the charger
- `https://data.v3.go-e.io` (optional) for the graphical charts and changelog in the firmware update screen

The app currently relies on system certificates and is to a certain point compatible with HTTPS MITM proxies in some corporate networks, although we highly discourage such practices.

