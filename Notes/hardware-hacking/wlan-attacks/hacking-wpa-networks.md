# Hacking WPA Networks

## Introduction

When connecting a device to a [WPA](../../networking/protocols/wlan-ieee-802.11/wifi-protected-access-wpa.md) WiFi network, the device and the access point go through the process of a [4-way handshake](../../networking/protocols/wlan-ieee-802.11/authentication-and-association.md). During this time, the hash of the password is broadcasted and if we can capture this hash, we can also attempt to crack it.

## Capturing the Handshake

You will first need to put your adapter into [monitor mode](../../index.md):

```
sudo airmon-ng start <dev>
```

![](<../../Hardware Hacking/Wireless Attacks/Resources/Images/WIFI\_airmon\_start.png>)

Next, you should listen for the available access points by using

```
sudo airodump-ng <dev>
```

![](<../../Hardware Hacking/Wireless Attacks/Resources/Images/WIFI\_airodump\_monitor\_all.png>)

Once you have identified the network you want to attack, you can make `airodump` specifically listening for it by providing a MAC address (`--bssid`) and a channel (`-c`). You will also want to write the capture to a file (`--write <filename>`), so that it may later be cracked with `aircrack-ng`:

```
sudo airodump-ng --bssid 50:D4:F7:95:CE:13 -c 11 --write PwnMe
```

![](<../../Hardware Hacking/Wireless Attacks/Resources/Images/WIFI\_airodump\_monitor\_single.png>)

Now, `airodump` is listening for the specified access point. Under the `STATION` tab, you can see all devices which are connected to the network.

You now have to wait for someone new to connect to the target network or to reconnect in order to capture the handshake. If you are too impatient, however, there is a way to speed this process up. A [deauth attack](deauth-attack.md) may be used, giving us the handshake:

![](<../../Hardware Hacking/Wireless Attacks/Resources/Images/WIFI\_handshake\_captured.png>)

![](<../../Hardware Hacking/Wireless Attacks/Resources/Images/WIFI\_airodump\_list\_capture\_files.png>)

The hash can now be cracked using `aircrack-ng`:

```
aircrack-ng <capture> -w <wordlist>
```

![](<../../Hardware Hacking/Wireless Attacks/Resources/Images/WIFI\_aircrack.png>)

Boom! We successfully cracked the very difficult-to-guess password of... `password`.

Remember to stop your adapter's monitor mode or you will not be able to use it normally:

![](<../../Hardware Hacking/Wireless Attacks/Resources/Images/WIFI\_airmon\_stop.png>)
