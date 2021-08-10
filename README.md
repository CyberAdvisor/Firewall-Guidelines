# Firewall Guidelines

This guidance is intended for those who are setting up new firewalls for small enterprises or home pro environments. It assumes the use of low-cost commercial firewalls that are common in that environment such as pfSense, OPNsense, Sophos, etc.  The guidance below assumes pfSense as the default. If you are not familiar with how to implement these instructions, please refer to the excellent online documentation for pfSense available at https://docs.netgate.com/pfsense/en/latest/. If you would like more specific instruction on how to implement this guidance, I recommend my book on the subject at https://www.amazon.com/Safer-Home-pfSense®-internet-experience-ebook/dp/B089618VB3/.

The guidance is also based on a simple, somewhat generic environment. For business in particular you will need to customize it based on the particular requirements of your business for segmentation, permanent VPN tunnels, and web filtering, none of which are included below.

## Initial Setup

1. Block all IPV6 unless specifically needed for a particular product or network requirement.
2. Ensure that email alerting is set up in the firewall so that you can receive alerts and reports.
    - https://docs.netgate.com/pfsense/en/latest/config/advanced-notifications.html#smtp-e-mail
3. Setup regular backups of the firewall configuration. 
    - https://docs.netgate.com/pfsense/en/latest/backup/autoconfigbackup.html
4. Setup NTP on the firewall to sync to known good external sources. All internal devices should sync to the firewall for NTP. 
    - https://docs.netgate.com/pfsense/en/latest/config/setup-wizard.html#ntp-and-time-zone-configuration
    - https://docs.netgate.com/pfsense/en/latest/services/ntpd/server.html#ntp-server-configuration

## DNS

1. Setup DNS resolution to use a TLS enabled 3rd party such as Cloudflare, Quad9, Cisco, etc. I do not recommend using the ISP default provided. Depending on your choice, you can select a vendor that provides malware and/or adult filtering capabilities. 
2. Setup the DNS resolver in the firewall to forward to the external resolver using DNS over TLS, and enable DNSSEC if supported by the external resolver.

## Segmentation

1. Setup a separate subnet for IoT traffic to isolate it from the main compute LAN. For small businesses, this would be the guest WiFi which should be isolated from the business LAN. If you have both guest and internal Wifi networks, you will need switches and access points that support vLANs. 
2. Use a VLAN or dedicated port/switch setup to segment the traffic.
3. Setup firewall rules to isolate the IoT network from the LAN, and the LAN from the IoT network (except for those IPs which will be used to manage the IoT network devices, if needed). 

## DHCP

1. Setup static DHCP addresses for all devices, as needed, to support future troubleshooting. For business environments, usually, this is only applied to the LAN, with the Guest/IoT network having dynamic IP assignments.

## Performance

1. Depending on the internal to external line speed disparities, you may need to implement fixes for bufferbloat. This typically occurs in environments when a high-speed local network is connected to a lower speed external network, especially one that is asymmetric (100mb down, 1mb up). 

## Block Access to Bad Actors

1. Leverage DNS and IP block lists to block access to known bad sites on the internet, from internal devices which may be compromised by malware or by internal user browsing. Not all firewalls support this capability - pfSense does. 
2. Leverage DNS and IP block lists to block access to TOR nodes from internal systems, unless this is specifically needed.
3. Leverage DNS and IP block lists to block access to DNS over HTTPS DNS Servers from internal systems, unless this is specifically needed, as DoH allows users to bypass whatever web filter you have enabled, as well as is used by some malware to hide C2C communications.
4. Leverage GEO blocking capabilities of the firewall to block access from the LAN to countries known to be the source of large numbers of attacks and malware unless specifically needed. 

## Harden Configuration

1. Block/redirect all internal DNS and NTP traffic back to the firewall for resolution and processing. 
2. Block all IP traffic using protocols that should not leave the internal network (such as SMB and FTP), unless specifically needed.
3. Disable (or make sure not enabled) UPnP & NAT-PMP unless specifically needed. 
4. Make sure SSH access is not enabled. If needed, only enable from the inside interface (never from the WAN), and disable when no longer needed. 
5. Enforce HTTPS access to the firewall itself. 

## Monitoring

1. Set up daily reports on firewall logs and blocking statistics to monitor for internally compromised systems. I typically tune my rules to minimize logging, so that the only items that are logged are those that require investigation. Logging everything just generates noise and makes it hard to find real issues that need to be addressed. 
2. Setup monitoring for new devices being added to the network on either the LAN or the LAN/IoT segments to ensure that rogue devices are not being added. 

And that's it for the initial base configuration. From there, you can customize as needed to meet the particular needs of your business or home environment. 






