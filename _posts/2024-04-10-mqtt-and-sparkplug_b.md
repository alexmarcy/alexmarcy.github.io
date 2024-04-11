---
layout: post
title: MQTT and Sparkplug B
subtitle: The differences between standard MQTT and the Sparkplug B protocol
categories: mqtt-101 sparkplug-b
description: Learn how the Sparkplug B specification adds some additional structure to the MQTT protocol to make industrial data a little easier to manage compared to standard MQTT, as well as the tradeoffs of using Sparkplug B.
headline: Learn how the Sparkplug B specification adds some additional structure to the MQTT protocol to make industrial data a little easier to manage compared to standard MQTT, as well as the tradeoffs of using Sparkplug B.
image: /assets/images/mqtt-and-sparkplug-b.png
tags: [mqtt-101, sparkplug-b]
author: MQTT Mafia
---

In our post [explaining MQTT]({% link _posts/2024-04-05-what-is-mqtt.md %}) we are referring to the "standard" MQTT specification. This version of the specification is generalized and can be applied to any use case.

The specifics of a topic format are up to the user to manage, and the payloads you can send over are essentially free form.

This is great for general MQTT adoption, making it flexible enough for almost anyone to use MQTT in their project.

In the mid 2010's when Cirrus Link released their Ignition Modules this flexibility became a burden for some users.

Integrated with Ignition, the major use case of MQTT was sending time series data for particular data points, or tags. In the Industrial data world tags are the main use case for data coming from PLCs and going into SCADA systems.

While MQTT can easily support this with the existing topics and namespaces there was a big opportunity to make everyone's life easier and make MQTT a compelling choice for Ignition users.

## Enter Sparkplug B

At it's core Sparkplug B is a specification detailing how data is to be structured when it is integrated using MQTT.

Instead of giving you free reign to design any topic namespace you desire and any payload format you want, it defines a specific topic and payload structure optimized for time-series data.

It does approach this structure with the concept of edge devices as the primary source of data being sent over MQTT, so there can be some things you need to work around if you are going to use Sparkplug B inside of a single facility.

One major benefit of this approach in the Ignition ecosystem is Ignition can leverage the known format of Sparkplug B Topics and payloads to automatically generate tags in Ignition once the data is sent over the wire to an MQTT broker.

The one caveat to all of this is while Sparkplug B uses the foundation of MQTT to send data, it also uses Google Protobufs (Protocol Buffers) to encode the data into a more efficient package before sending it. Think of this in vey basic terms like zipping up a file to make it smaller before emailing it. This means you can't use existing, non-Sparkplug B compliant brokers to view the data without performing an additional step to decode the data. Thankfully most of the brokers on the market support Sparkplug B now so you don't have anything to worry about unless you are writing your own broker from scratch.

## The Sparkplug B Format

Sparkplug B gives you efficient data transfer, using a specified a known format specifically designed for industrial, time-series data. What does that look like?

The pre-defined topic namespace for Sparkplug B data is:
~~~
spBv1.0/Group ID/Message Type/Edge Node ID/Device ID
~~~

In practice (ignoring the standardized portions of spBv1.0 and Message Type), this boils down to:
~~~
Group ID/Edge Node ID/Device ID
~~~

(spBv1.0 specifies this is the Sparkplug B protocol, and Message Type is pre-defined by the spec, indicating what type of information is being sent and what type of node, device, or application is being used. Those matter if you are writing your own integration, but don't apply to users configuring devices to send data)

The payload format is also pre-defined by the specification:
~~~
{
    "timestamp": 1486144502122,
    "metrics": [{
        "name": "My Metric",
        "alias": 1,
        "timestamp": 1479123452194,
        "dataType": "String",
        "value": "Test"
    }],
    "seq": 2
}
~~~

The major difference with Sparkplug B's payload format and standard MQTT is Sparkplug B lets you send multiple data points in each message compared to MQTT's one point per topic format.

The overall reason behind this is you want to send a lot of data from one edge device in one block to take advantage of the data encoding and it also makes your life easier when consuming the data.

Instead of subscribing to 100 different topics from one device to get 100 data points, you can subscribe to one topic and get all 100 data points. The one gotcha is you will only be able to subscribe to the topic and get all of the data points when using Sparkplug B so if you want to filter some of them out there are other steps you will need to take.

## Ignition and Sparkplug B

Arguably the most powerful use case for Sparkplug B is integrating your data into the Ignition SCADA platform.

Using the Cirrus Link MQTT Engine Module, Ignition will take any Sparkplug B topics you subscribe to and automatically generate a hierarchical tag structure for you once the device sends data to the broker.

This means you can set up your Ignition screens, UDTs, and templates based on your overall naming conventions across your systems and sites, then as sites or devices come online and send data, all of your tags are automatically generated and your screens will be populated with data.

In a great post by some of the MQTT Mafia members at Corso Systems, [MQTT and Sparkplug B Simplified](https://corsosystems.com/posts/mqtt-and-sparkplug-b-simplified), they take a deeper dive into how subscribing to specific topics and data points works in standard MQTT and how it is different (and simpler for industrial data) in Sparkplug B.

## Wrapping Up
For industrial data, Sparkplug B has some amazing advantages over standard MQTT.

You can send a lot of data with even less bandwidth thanks to the data encoding and compression built in the specification. You can subscribe to a single topic and receive a lot of data without having to manage topics and data in a 1:1 format, and you can even leverage your existing tools like Ignition to manage a lot of data mapping for you by automatically generating tags.

Using Sparkplug B does not preclude you from using Standard MQTT either, you can do both in parallel for the times you need some of the regular functionality as well.
