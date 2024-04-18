---
layout: post
title: MQTT Pub/Sub
subtitle: What makes MQTT special among industrial communication protocols also enables it to be lightweight, let's discuss how that is possible!
categories: mqtt-101 pub-sub
description: The Pub/Sub model in MQTT makes it unique among industrial communication protocols. It also enables MQTT to be low bandwidth and lightweight. This post will explain what all of that means and why Pub/Sub is important.
headline: The Pub/Sub model in MQTT makes it unique among industrial communication protocols. It also enables MQTT to be low bandwidth and lightweight. This post will explain what all of that means and why Pub/Sub is important.
image: /assets/images/mqtt-pub-sub.png
tags: [mqtt-101, pub-sub]
author: MQTT Mafia
---

In our post [What Is MQTT?]({% link _posts/2024-04-05-what-is-mqtt.md %}) we briefly touched on the Publish/Subscribe (Pub/Sub) architecture in MQTT. In this post we will go into a lot more detail on what Pub/Sub is, how it works, and why it enables MQTT to be one of the lightest weight and lowest bandwidth protocols available today.

## Typical Protocols: Polling

Most industrial protocols, including OPC-UA, Modbus, Ethernet/IP, Profinet, and other proprietary ones use a polled architecture. This means some number of clients are polling servers on a cyclic basis. In manufacturing this is commonly a 1 second interval. This means every second the client is requesting data from the server. The server replies to the request, sends data out to the client, and the process repeats every second until the system is shutdown.

This works fine for "small" systems with 5,000-50,000 data points or tags, and a relatively small number of clients. As the number of tags grows and the number of clients using the data increases you can increase the resources available to the server to get more data flowing, although at some point you are going to run into the major bottleneck, your network infrastructure.

A polled architecture requires 2 way traffic. The clients send requests for data to the servers across the network, the servers send data back to the clients on the network, and there is only so much room available for data transfer over any given period of time.

As your system grows you will start to see slowdowns on data as it gets buffered waiting for bandwidth on the network to move between the clients and servers.

You can build a clever architecture to split up the overall data flow across different network segments to give you more room for growth, and you can stage your polling cycles so you only request critical data at the fastest rate. You can poll data that might not change often on a longer period, but you will eventually run into data throughput issues at larger scale.

## Pub/Sub Protocols

In the MQTT world Pub/Sub immediately saves a lot of bandwidth across any given network segment by separating, or decoupling the clients and the servers. In an MQTT architecture the servers (Publishers) and clients (Subscribers) don't know one another even exist. The subscribers never need to talk to the publisher directly because everything goes through MQTT brokers. This saves on bandwidth because the bulk of the bidirectional traffic in a polled architecture doesn't exist.

With the exception of data you are writing from a subscriber to a publisher, say a change in setpoint for a pump, data goes from Publishers to the Brokers, and from the Brokers to the subscribers.

This decoupling another great benefit in addition to the publishers and subscribers not needing to know anything about one another.

### Time is Decoupled w/ MQTT

Time is decoupled in a Pub/Sub system, meaning you don't need to read data from its source to get the most up to date values. The most up to date values are simply the ones available to each subscriber since they were published when they last changed. This simplifies understanding a system because you always have the latest copy of the data available.

This also means if your subscriber goes offline they will not lose any information from the system as the latest values will still be available in the broker when the subscriber comes online.

## Benefits of Pub/Sub

In a future post we will talk more about MQTT Quality of Service (Qos) levels and how they integrate with the Pub/Sub model to ensure data gets where it needs to go. QoS helps manage situations where data is published by no subscribers are online to receive it and it could get lost if new messages are published before the subscribers come online.

Pub/Sub and MQTT's topic structure let subscribers filter down to only the data they need to subscribe to. This helps with overall bandwidth and system performance by limiting what data needs to be sent where. This is sort of limited with the [Sparkplug B]({% link _posts/2024-04-10-mqtt-and-sparkplug_b.md %}) where you can send multiple datapoints per topic, although Sparkplug B helps mitigate this by compressing the data sent across the network.

One of the main benefits of Pub/Sub over polled protocols is the overall potential for scalability. Since subscribers don't communicate directly with publishers the number of subscribers can grow exponentially across any given network with little impact to performance. You can even set up multiple brokers along with various clustering and load balancing methods to spread the flow of information around, optimizing your system as much as possible.

## Wrapping Up

Pub/Sub provides a great alternative to polled protocols for large scale systems. This doesn't necessarily mean you should never use polled protocols, they are still very relevant and the right choice in many cases. This is especially true within the four walls of a particular facility.

When you need to manage LOTS of data, especially over distributed networks MQTT's Pub/Sub model can save you a lot of bandwidth and network infrastructure costs, ultimately saving you money while providing access to all of the data you need to run your system most effectively.
