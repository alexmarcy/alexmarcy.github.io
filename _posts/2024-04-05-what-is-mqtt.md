---
layout: post
title: What is MQTT?
subtitle: The high level details of MQTT.
categories: mqtt-101
description: Learn the basics of MQTT. What it is, how it works, why you want to use it, and how it fits into your overall data management strategy.
headline: Learn the basics of MQTT. What it is, how it works, why you want to use it, and how it fits into your overall data management strategy.
image: /assets/images/mqtt-101.png
tags: [mqtt-101]
author: MQTT Mafia
---

MQTT is a communication protocol typically used to send data from various devices or equipment to other devices, equipment, or software systems. It was originally designed with oil and gas pump stations in mind.

The original goal of MQTT was to provide a way to send data from critical assets, typically located in not well connected locations, over slow networks, using as little bandwidth as possible.

This goal led to a few of the benefits of MQTT as a large scale communication platform compared to other communication protocols.

## What is a Communication Protocol?

A communication protocol is an agreed upon way to format data to send it from Point A to Point B and beyond. Typically a protocol will have a specification used by anyone who wants to implement it describing the format of data, any triggers they need to account for, how the data is transmitted, and what to do when data is received (for bi-directional protocols).

Common industrial protocols include Modbus, OPC, Ethernet/IP, Profinet, and MQTT.

Typically a hardware manufacturer will have a protocol their equipment supports natively. The first industrial communication protocol, Modbus, was developed by Modicon for their PLC hardware used in process controls applications.

Each protocol will specify how data is formatted when it is transmitted, what to do when it is received, and in the case of industrial data, how to "address" data so you know a particular value should be mapped to a given data point.

Industrial communication protocols thus form the foundation of "tags" in most SCADA systems where a tag is a particular datapoint with a user-readable name mapped to some value in the process. In the case of Modbus this will be something like 40301 which might map to Tank 101's level indicator.

In the case of MQTT this address is defined by the topic definition, something we will get into later on in this post.

## How Does MQTT Work?

Most of the industrial communication protocols are "polled" meaning a central server or software package will "poll" devices on a cyclic basis to read data. This requires the server to know what data it is looking for, send requests out to the various devices with the data, and request new values. The new values are pulled together by the device, then sent back to the server. This requires more bandwidth than something like MQTT, because the traffic is always bi-directional.

MQTT on the other hand uses a concept called publish/subscribe where devices will publish data to a central data broker, and users of the data can subscribe to the broker to get updated data as it is sent. This means in a lot of cases the data flow will only be one way cutting the overall bandwidth requirements significantly.

Data flow using MQTT can be bi-directional too, it simply is required to be.

MQTT has a few major concepts you will need to understand:

### MQTT Broker

The main component of any MQTT system is the MQTT Broker. This can be something you run on a server on your network, or it can be a service like HiveMQ or an open source alternative Mosquitto that runs in the cloud.

Some SCADA systems like Ignition support the Chariot Broker from Cirrus Link as part of their native set of tools, although you can use an MQTT broker you want in place of the Chariot Broker.

The broker is the central repository all of the data will pass through. You can use multiple brokers in an architecture, and they can be connected together, but for simplicity we will just look at a single broker for this post.

### MQTT Client

MQTT Clients are the various devices, services, or users of your data. A client can be a field device like a PLC or a sensor, it can be a software platform like Ignition, or a database, or it could be a website capable of connecting to an MQTT broker directly.

Clients can generate data to send to the broker, they can connect to the broker to subscribe to data from other clients, or they can read and write data as needed. While most systems will have one or a few brokers, they may have hundreds or thousands of clients.

In the original use case for oil/gas pump stations each pump station would be an MQTT client, sending and receiving data from the broker as it became available.

### MQTT Topic

For a client to send data to a broker it needs to be mapped to an MQTT Topic. A topic is a user-definable "address" for lack of a better word mapping out where the data came from and what it represents.

A typical MQTT topic will have some form of naming convention with various "levels" separated by slashes. Each level will represent something at that level of the hierarchy, with each subsequent level getting more granular down to an individual datapoint.

For the oil/gas example some topics might be:

spike_ridge/pump_station/motor_001/speed
spike_ridge/pump_station/well/level
spike_ridge/pump_station/door_alarm

These 3 data points all come from the Spike Ridge site, are related to the Pump Station at that site, and refer to various signals related to the pump station itself. In this case we have the speed for Motor 001, the well level, and a door open alarm for the pump station building.

The device at the pump station sending data would publish data using these topics to the MQTT broker at the main control station. Any signals like setpoints or alarm acknowledgement would be sent back on relevant topics, and the SCADA system along with any other clients needing data from Spike Ridge could subscribe to these topics to get data as it is published.

Clients can subscribe to the specific values if they only need the motor speed, or they can subscribe to the entire spike_ridge/pump_station topic, or the spike_ridge site level using wildcards for the lower levels in the topic to get all of the relevant data.

Data can also be packaged up into various formats to send multiple datapoints for motor_001 as a JSON string or in other formats if needed, although we will save that for a future post. Topics can also be used to send files and many other datatypes, although we'll save that for a future post too.

## What Makes MQTT Great?

While MQTT was originally developed with a focus of reducing bandwidth to send industrial data across a network, it has grown into a powerhouse of the industrial automation world with many of the original design goals making it easy for MQTT to scale to some of the largest systems in the world!

### Lightweight and efficient
MQTT was designed to be lightweight and efficient in its use of bandwidth. While this is great for remote sites with intermittent cell connections, it works even better at large scale with always connected systems by reducing the network bandwidth for all data.

This low bandwidth design coupled with the pub/sub architecture allows MQTT to handle massive amounts of data from hundreds or thousands of devices on a high bandwidth connection. This is enabled the rapid expansion of cloud-based systems like Amazon Web Services built on MQTT as a primary data source.

### Secure
As a protocol built on the TCP/IP network stack, MQTT has security built in. This allows you to use certificates to enable secure and encrypted connections. MQTT also lets you authenticate your MQTT clients ensuring only authorized users can access your data.

### Reliable
The original design goal of enabling sites with un-reliable cell service makes MQTT one of the most robust communication protocols available.

MQTT gives you the ability to set up various Quality of Service levels to ensure your data makes it where it needs to go with confirmation for the most critical data.

Many platforms also layer on store and forward capabilities giving you a very powerful set of tools ensuring your valuable data gets where it needs to go, even in the most critical applications.

### Scalable
MQTT is highly scalable, capable of handling the largest systems known to humanity. Some systems even have clients in the millions. While you need a powerful network to handle this, and potentially some decisions about how often some of your data is published, MQTT is capable of supporting the largest systems possible compared to other protocols.

This is all because of the original design goals focused on building the smallest resource usage protocol possible.

### Open and Well Supported

Unlike some of the other protocols requiring expensive investment into the various foundations supporting them, MQTT is an open platform with freely available specifications.

This means any person on the planet can download the specifications and build MQTT compliant hardware and software.

## Wrapping Up

MQTT is a great communication protocol. It is capable of handling massive amounts of data in complex formats. It is open and reliable, and is in use by some of the biggest companies in the world.

It is also completely suitable and widely used by people for smaller projects like home automation because it is freely available and supported at every level of the industrial information technology stack.

We'll dive deeper into these topics and more in future posts! Thanks for following along.
