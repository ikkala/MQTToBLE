MQTToBLE
========

This repo contains a set of tools that you can use to set up a simple, scalable, MQTT over BLE implementation.


How it works
------------

You have:

* **MQTToBLE nodes** - low power Bluetooth LE devices that want to connect
* **MQTToBLE bridge** - powered Bluetooth LE devices with internet connections. These communicate with **MQTToBLE nodes** and connect to a **MQTToBLE server**
* **MQTToBLE server** - a server with an internet connection. This controls everything, choosing the nearest **MQTToBLE bridge** and telling it to connect to a **MQTToBLE node**

### Summary:

* Each **MQTToBLE node** implements the MQTT protocol, and then uses BLE to transport the MQTT datastream.
* A **MQTToBLE node** spends 99% of its time as a normal BLE advertiser.
* An **MQTToBLE bridge** will connect to a **MQTToBLE node** when there is data to be sent to is *or* the **MQTToBLE node** advertises that it has data it needs to send. The bridge will then disconnect after a short period of inactivity.


Each **MQTToBLE node** advertises the following 128 bit Service UUIDs:

* `ac910000-43be-801f-3ffc-65d26351c312` - Implements MQTToBLE, but no data to send
* `ac910001-43be-801f-3ffc-65d26351c312` - Implements MQTToBLE, there is data ready to send

Each **MQTToBLE node** also has the following BLE service and characteristics (much like Nordic UART):

* Service `ac910001-43be-801f-3ffc-65d26351c312`
* TX Characteristic `ac910002-43be-801f-3ffc-65d26351c312` - from bridge TO node
* RX Characteristic `ac910003-43be-801f-3ffc-65d26351c312` - from node TO bridge


Security
--------

Currently this is not implemented, however:

* An **MQTToBLE bridge** could bond with every device it connects to
* A PIN could be added to **MQTToBLE nodes** and the **MQTToBLE server** could be made aware of the PINs of all enrolled devices.

Bridge -> Server comms
----------------------

These are also done over MQTT.

* Each bridge has a name
* `MQTToBLE/{bridgename}/advertise` `{addr:str, rssi:int, dataReady:bool}` is sent from the bridge for each advertisement received
* To connect, server sends `MQTToBLE/{bridgename}/tx` `{addr:str, data:str}` to the bridge, which can contain no data. Data to be sent must be chunked into 20 byte chunks already
* When data is received, the bridge sends `MQTToBLE/{bridgename/rx}` `{addr:str, data:str}` to the server with any received data packets
