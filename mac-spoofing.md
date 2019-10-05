Title:      MAC Spoofing
Topic:      privacy
Author:     osaka
Date:       11/02/2014
Source:     https://nationpigeon.com/mac-spoofing/
Course:     

There are times when you want to change your MAC address, be it for fun, by-passing time restrictions or for development reasons.

For Linux there is a nice small tool which does this for you. It’s called macchanger. You can download and compile it or install it with your package manager.

For example, on Debian/Ubuntu you can do:

```
sudo apt-get install macchanger
```

Make sure you are not connected to the network first, and bring down your interface. In this case it’s my wireless interface, you may want to use your ethernet, which might be eth0.

```
sudo ifconfig wlan0 down
```

Now we change the end of the MAC address which will change your address but keep the same vendor bytes. For more information about this read up on MAC addresses

```
sudo macchanger -e wlan0
```

Now bring the interface back up, and connect to the network using your network manager.

```
sudo ifconfig wlan0 up
```

Check if we still have the new address.

```
macchanger wlan0
```

You should see something like this, if your current address is different then all is good.

```
Permanent MAC: 00:2F:77:36:57:F4 (Intel Corporate)
Current   MAC: 00:2F:77:F4:CE:02 (Intel Corporate
```