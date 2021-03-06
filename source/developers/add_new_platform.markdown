---
layout: page
title: "Adding support for a new platform"
description: "Hints and tips for when you're adding a new platform to Home Assistant."
date: 2014-12-21 13:27
sidebar: false
comments: false
sharing: true
footer: true
---

Components that interact with devices are structured in core- and platform logic. This allows the same logic to be used for different platforms.

For example, the built-in `switch` component consists of the following files in [`homeassistant/components/switch/`](https://github.com/balloob/home-assistant/tree/master/homeassistant/components/switch):

| File | Description |
| ---- | ----------- |
| \_\_init\_\_.py | Contains the Switch core logic.|
| wemo.py | WeMo platform logic. Included if in config `platform=wemo`. |
| tellstick.py | Tellstick platform logic. Included if in config `platform=tellstick`. |

If you are planning to add support for a new type of device to an existing component, you can get away with only writing platform logic. Have a look at how the component works with other platforms and create a similar file for the platform that you would like to add.

<p class='note'>
Platform logic should not interface directly with the devices but use a third-party Python 3 library that speaks the actual API.
</p>

<a name='discovery'></a>
## {% linkable_title Allowing your platform to be discovered %}

Home Assistant has a discovery service running in the background to discover new devices. Whenever a new device is discovered, an `SERVICE_DISCOVERED` event will be fired with the found service and the information. The `discovery` component has some knowledge about which components handle which type of services and will ensure those are loaded and listening before firing the `SERVICE_DISCOVERED` event.

### {% linkable_title Add discovery instructions %}

Device discovery  for Home Assistant has been extracted into an external library called [NetDisco](https://github.com/balloob/netdisco). This library is integrated using [the `discovery` component](https://github.com/balloob/home-assistant/blob/dev/homeassistant/components/discovery.py) and scans the network in intervals for uPnP and zeroconf/mDNS services.

To have your device be discovered, you will have to extend the NetDisco library to be able to find your device. This is done by adding a new discoverable. [See the repository for examples of existing discoverables.](https://github.com/balloob/netdisco/tree/master/netdisco/discoverables)

### {% linkable_title Listening to `SERVICE_DISCOVERED` events %}

From your component, you will have to set up the listening for specific services. Below an example how one would listen for discovered Chromecasts:

```python
from homeassistant.loader import get_component

def setup(hass, config):
    discovery = get_component('discovery')

    def chromecast_discovered(service, info):
        """ Called when a Chromecast has been discovered. """
        print("Discovered a new Chromecast: {}".format(info))

    discovery.listen(
        hass, discovery.services.GOOGLE_CAST, chromecast_discovered)
```

### {% linkable_title Auto-loading your component upon discovery %}

The Discovery component is capable of setting up your components before firing the `SERVICE_DISCOVERD` event. To do this you will have to update the [`SERVICE_HANDLERS`](https://github.com/balloob/home-assistant/blob/dev/homeassistant/components/discovery.py#L29) constant in [the `discovery` component](https://github.com/balloob/home-assistant/blob/dev/homeassistant/components/discovery.py).

<p class='note warning'>
This option is currently limited to built-in components.
</p>
