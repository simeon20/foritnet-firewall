File & Directory Checks

These first commands are checking for the existence, size, and permissions of specific files or directories in the FortiGate’s filesystem.

fnsysctl ls -la /data/lib/libips.bak

    Runs ls -la (list files with details) inside FortiGate's underlying OS using fnsysctl.

    Checking if the file /data/lib/libips.bak exists and gathering info like owner, permissions, size, and timestamp.

The rest are the same concept, just for different files:

fnsysctl ls -la /data/lib/libgif.so      # Checks for GIF processing library
fnsysctl ls -la /data/lib/libiptcp.so    # Checks for TCP-related processing library
fnsysctl ls -la /data/lib/libipudp.so    # Checks for UDP-related processing library
fnsysctl ls -la /data/lib/libjepg.so     # Likely typo of 'libjpeg.so', checks JPEG processing library
fnsysctl ls -la /var/.sslvpnconfigbk     # Hidden backup config for SSL VPN
fnsysctl ls -la /data/etc/wxd.conf       # WX daemon configuration file
fnsysctl ls -la /flash                   # Lists the contents of the flash storage root

💡 Purpose: Likely part of a forensic or compromise check—these paths could be used by malware on FortiGate devices.
Firewall Address Object Creation

These commands create individual firewall address objects for specific IPs.

config firewall address

    Enters address configuration mode.

edit 188.34.130.40
set color 6
set type ipmask
set subnet 188.34.130.40/32
next

    edit <name>: Starts editing or creating an object named 188.34.130.40.

    set color 6: UI color label for organization in FortiGate's GUI.

    set type ipmask: Defines this as an IP address or subnet.

    set subnet 188.34.130.40/32: Assigns the IP address (with /32 mask for a single host).

    next: Ends this address object’s configuration.

Same logic for:

edit 103.131.189.143
...
edit 192.36.119.61
...
edit 172.247.168.153
...
end

Creating a Group for Malicious IPs

config firewall addrgrp
edit "Malicious IPs"
set color 6
append member "188.34.130.40" "192.36.119.61" "172.247.168.153" "103.131.189.143"
next
end

    config firewall addrgrp: Enters address group configuration mode.

    edit "Malicious IPs": Creates or edits a group named Malicious IPs.

    set color 6: Assigns a GUI color for quick identification.

    append member ...: Adds the previously created address objects to the group.

    next / end: Saves the group.

Adding the Malicious IP Group into Another Group

config firewall addrgrp
edit "Geo_Block"
append member "Malicious IPs"
end

    Edits an existing group Geo_Block (likely blocking entire countries/regions).

    Adds Malicious IPs group into it—meaning rules referencing Geo_Block will also block these malicious IPs.

    Ends the edit.

✅ Summary of Purpose:

    File checks – Possibly investigating suspicious system files or confirming integrity on a FortiGate.

    Firewall configuration – Creates address objects for malicious IPs, groups them together, and includes them in a broader Geo_Block policy for blocking.

Quick read:

    Each /32 IP = individual address object

    Those get grouped into “Malicious IPs” (addrgrp)

    “Geo_Block” includes “Malicious IPs”

    Any firewall policy that references Geo_Block will match (and block/allow per the policy action) those IPs. 
