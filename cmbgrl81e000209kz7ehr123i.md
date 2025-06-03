---
title: "Getting Started with Apache Kafka in Kraft Mode"
datePublished: Tue Jun 03 2025 17:00:14 GMT+0000 (Coordinated Universal Time)
cuid: cmbgrl81e000209kz7ehr123i
slug: getting-started-with-apache-kafka-in-kraft-mode
tags: data-science, big-data, windows, apache, kafka, apache-kafka, zookeeper, bigdata-analytics-datascience-ai-machinelearning-iot-iiot-python-rstats-tensorflow-javascript-reactjs-cloudcomputing-serverless-datascientist-linux-programming-coding-100daysofcode, apache-zookeeper, kraftmode, datapipelines

---

*By Pavit Kaur*

When I first heard about Kafka, it sounded intense.  
Distributed systems, message brokers, real time data pipelines, the kind of stuff you imagine running in the background of big tech companies.

But curiosity got the better of me. I decided to set it up on my own machine, and to my surprise? It wasn’t that hard at all.

In this blog, I’ll walk you through a short history of Kafka and ZooKeeper, how **KRaft** simplifies things, and most importantly, **how I set it all up on Windows**, step by step.

## So what exactly is Kafka?

**Apache Kafka** is data streaming platform used to build **data pipelines and streaming apps**. Kafka lets you **send, store, and process data streams** , like logs, user activity, payment transactions, sensor data in real time across the systems.

## Why do we need it?

Kafka basically helps different parts of a system talk to each other smoothly, this is helpful when dealing with data. Suppose you have a system that collects user data, another that processes it, and a third that stores it. Kafka helps you **decouple** these systems so they can work independently and scale easily.

As per what I have searched, kafka is helpful in at the moment data analytics, for example in streaming data. If there is real time data coming in, we can take the data using kafka. It works as a center for data monitoring and data analysis. It has features such as data mirroring by creating replications, and thus prevents data loss by having data backup.

## Where is it used?

Kafka is currently used in quite a lot of big tech companies around the world. Here are a few real-world examples:

* **LinkedIn** — This is actually where Kafka was created. They use it to track everything, from user activity to system performance across their platform.
    
* **Netflix** — Kafka handles massive streams of events, like user playback actions, errors, and recommendations. It helps them monitor and improve user experience in real time.
    
* **Uber** — Kafka plays a major role in their real time systems, especially in tracking driver and rider locations, trip updates, and fare calculations.
    
* **Spotify** — Relies on Kafka to deliver personalized music recommendations and monitor how users interact with their app.
    
* **Goldman Sachs & other banks** — Use Kafka for real time trade processing, fraud detection, and risk monitoring.
    
* **Twitter** — Streams tweets and analytics data through Kafka for internal processing and user facing features.
    

In short, it is used anywhere data needs to move fast, stay reliable, and power systems that need to react instantly — whether that’s showing you the next song, updating a map, or catching a security issue.

## A bit of a Kafka History Lesson

In the past, Kafka couldn’t run on its own. It needed help from another tool called **ZooKeeper**.

ZooKeeper helped Kafka do important things like:

* Choosing a leader when multiple servers were running
    
* Keeping track of topics and their configurations
    
* Checking which Kafka servers (called brokers) were online
    

But using ZooKeeper also made things harder:

* You had to install and manage it separately, so there was more setup
    
* It made the system more complex overall
    
* And when Kafka started handling more data at scale, ZooKeeper sometimes couldn’t keep up
    

So while ZooKeeper was useful in the beginning, it eventually became a **problem for scaling and simplicity** and that’s why Kafka moved away from it in later versions.

## Enter KRaft- Kafka Raft Mode

Starting from **Kafka 2.8** (early preview) and **Kafka 3.3+ (production ready)**, Kafka introduced **KRaft mode** which is a **ZooKeeper less architecture**.

## What is KRaft?

This new mode is called **KRaft**, which stands for **Kafka + Raft** it replaces ZooKeeper with a built in system based on the **Raft consensus algorithm**.

In simple terms, KRaft allows Kafka to **manage everything by itself**, without needing an external tool like ZooKeeper. It stores and replicates important metadata such as broker status, topic settings, and leader elections directly inside Kafka.

#### KRaft had several advantages such as,

* No need of separate Zookeeper installation, thus simpler setup, easier to deploy and maintain.
    
* There is better fault tolerance since KRaft keeps metadata consistent even if some servers fail. The metadata stays in the Kafka so there is no more splitting between two system.
    
* It is useful for developers who want a lean, modern, and manageable data streaming setup.
    

## Setting Up Kafka with KRaft on Windows

Now that we’ve covered the backstory of Kafka and why it moved away from ZooKeeper, let me take you through the technical (but simple) set up process.

### Prerequisites:

Before we start, make sure you’ve got the following ready:

* **JDK 17 or higher** installed
    
* **Kafka for Windows** (version 3.5 or later is ideal)
    
* (Optional but helpful) **Java and Kafka added to your system PATH** so you can run commands from any terminal window
    

**1\. Download & Extract Kafka**

First, [download the Kafka binary](https://kafka.apache.org/downloads) for your version (e.g., 3.9.1) and extract it somewhere on your system.

You will now have a folder containing all the tools you will require, such as the scripts to start the server, create topics, and more.

For example:

```powershell
C:\kafka\kafka_2.13-3.9.1
```

* `2.13`: Refers to the Scala version Kafka was compiled with
    
* `3.9.1`: Is your Kafka version.
    

Inside this folder, you’ll find folders such as `bin`, `config`, `libs`, which are the tools required to run Kafka.

#### 2\. Format Metadata (Need to do this only once!)

Kafka in KRaft mode needs to initialize a special internal log to store metadata. You only have to do this **once** before starting the broker.

Run this command in PowerShell:

```powershell
.\bin\windows\kafka-storage.bat format -t <UUID> -c .\config\kraft\kraft-broker.properties
```

**What does this do?**

* `kafka-storage.bat`: A script to initialize Kafka’s internal metadata storage
    
* `format`: This tells Kafka to prepare the disk for metadata logging. It's like creating a "blank notebook" where Kafka can write critical info (like topic configs, cluster state, etc.)
    
* `-t <UUID>`: The UUID (Universally Unique Identifier) is a special ID that *uniquely identifies your Kafka cluster*. All brokers in the same cluster must use the same UUID.
    
* `-c .\config\kraft\`[`kraft-broker.properties`](http://kraft-broker.properties): This points to the config file that defines how your broker behaves, with port, data directory, log retention, etc.
    
    Generate a UUID using this command, then copy it and paste it in place of `<UUID>` above.
    

```powershell
[guid]::NewGuid()
```

#### 3\. Start Kafka Broker

Now that your metadata is set up, it’s time to launch Kafka itself:

```powershell
.\bin\windows\kafka-server-start.bat .\config\kraft\kraft-broker.properties
```

#### What This Does:

* `kafka-server-start.bat`: This is the main script to start a Kafka brokers.
    
* You pass it a config file — in this case, [`kraft-broker.properties`](http://kraft-broker.properties), which enables **KRaft mode** instead of ZooKeeper.
    

Kafka reads that config and starts the broker, including:

* Opening port 9092 to listen for clients
    
* Starting internal threads for managing messages, topics, replication
    

You’ll see a ton of logs as Kafka boots up. Once it stabilizes, your broker is ready to handle topics and messages.

### Now that your basic setup is done, let us send messages by the producer, and receive messages by the consumer.

1. ### Create a Topic:
    

```powershell
.\bin\windows\kafka-topics.bat --create --topic testtopic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

Breakdown of the command:

* `--create`: This tells Kafka you want to create a new topic.
    
* `--topic testtopic`: I created a new topic named “testtopic” you can keep it anything you want
    
* `--bootstrap-server` [`localhost:9092`](http://localhost:9092): Points to the Kafka broker that will handle the request. [`localhost:9092`](http://localhost:9092) is the default. You can change this incase that port is busy.
    
* `--partitions 1`: A topic is divided into partitions. More partitions means more parallelism, but for local testing, one is fine.
    
* `--replication-factor 1`: This means you only want one copy of the topic's data which is fine for single node setups. In production, this would usually be 2 or 3 as data backups.
    

2. ### Start a Producer (sends messages):
    

```powershell
.\bin\windows\kafka-console-producer.bat --topic testtopic --bootstrap-server localhost:9092
```

Once you run this, Kafka opens a console where you can **type messages** and every time you hit Enter, it sends that line as a new message to `testtopic`.

It’s like a chat window where messages go into Kafka’s topic instead of being sent to a person.

3. ### Start a Consumer (receives messages):
    

```powershell
.\bin\windows\kafka-console-consumer.bat --topic testtopic --from-beginning --bootstrap-server localhost:9092
```

* `kafka-console-consumer.bat`: This opens a message reader (consumer) that subscribes to the topic.
    
* `--topic testtopic`: the topic name to read from (change it as per what you wrote above)
    
* `--from-beginning`: By default, Kafka consumers only see **new messages** from the moment they start.  
    This flag tells it: “Give me **everything** from the start.”
    
* `--bootstrap-server`: Again, connect to the broker.
    

### In case you want to delete a topic:

```powershell
.\bin\windows\kafka-topics.bat --delete --topic testtopic --bootstrap-server localhost:9092
```

Remember that there is no undo, it’s gone for good unless you have a backup.

## Conclusion: What I Learned

Going into this, Kafka felt like one of those intimidating “big data” buzzwords, the kind you hear thrown around in tech talks but rarely see broken down in a beginner friendly way.

Now that I understand the basics, I’m excited to dig deeper into what it can do.

*Writing this blog is my way of learning by teaching. If you’re just starting out, I hope this helps you too!*