# 🚀 Deep Packet Inspection (DPI) Engine

A high-performance **C++17 Deep Packet Inspection (DPI) Engine** capable of parsing PCAP files, identifying applications using TLS Server Name Indication (SNI) and HTTP Host headers, tracking network flows, applying filtering rules, and generating filtered PCAP outputs.

The project contains both a **single-threaded implementation** for learning and a **multi-threaded architecture** designed for scalable packet processing.

---

# Features

* 📦 Read and parse PCAP files
* 🌐 Ethernet, IPv4, TCP, and UDP packet parsing
* 🔍 Deep Packet Inspection using:

  * TLS Client Hello (SNI Extraction)
  * HTTP Host Header Extraction
* 🔄 Stateful flow tracking using Five-Tuple
* 🚫 Block traffic by:

  * Source IP
  * Application
  * Domain Name
* 📊 Generate application-wise traffic reports
* ⚡ Multi-threaded processing pipeline
* 🧵 Thread-safe producer-consumer architecture
* 📁 Generate filtered PCAP output

---

# Supported Application Detection

The DPI engine currently identifies traffic for applications such as:

* YouTube
* Facebook
* Google
* HTTP
* HTTPS
* DNS

Additional signatures can be added easily.

---

# Project Structure

```text
packet_analyzer/
│
├── include/
│   ├── pcap_reader.h
│   ├── packet_parser.h
│   ├── sni_extractor.h
│   ├── types.h
│   ├── rule_manager.h
│   ├── connection_tracker.h
│   ├── load_balancer.h
│   ├── fast_path.h
│   ├── thread_safe_queue.h
│   └── dpi_engine.h
│
├── src/
│   ├── main_working.cpp
│   ├── dpi_mt.cpp
│   ├── pcap_reader.cpp
│   ├── packet_parser.cpp
│   ├── sni_extractor.cpp
│   └── types.cpp
│
├── generate_test_pcap.py
├── test_dpi.pcap
└── README.md
```

---

# Architecture

## Single Threaded Version

```
Input PCAP
     │
     ▼
PCAP Reader
     │
     ▼
Packet Parser
     │
     ▼
Flow Tracker
     │
     ▼
SNI / HTTP Inspection
     │
     ▼
Rule Engine
     │
     ▼
Filtered Output PCAP
```

---

## Multi Threaded Version

```
                 Reader Thread
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
     Load Balancer             Load Balancer
          │                         │
      ┌───┴───┐                 ┌───┴───┐
      ▼       ▼                 ▼       ▼
   FastPath FastPath        FastPath FastPath
      │       │                 │       │
      └──────────────┬──────────┘
                     ▼
              Output Writer
```

Each network flow is consistently hashed to the same FastPath thread, ensuring correct flow-state management without synchronization overhead.

---

# How It Works

For every packet, the engine performs the following steps:

1. Read packet from PCAP.
2. Parse Ethernet/IP/TCP/UDP headers.
3. Construct the Five-Tuple.
4. Lookup or create a flow.
5. Extract:

   * TLS Server Name Indication (SNI)
   * HTTP Host Header
6. Classify application.
7. Apply blocking rules.
8. Forward or drop packet.
9. Generate processing statistics.

---

# Blocking Rules

The engine supports three kinds of filtering:

| Rule        | Example        |
| ----------- | -------------- |
| IP Address  | `192.168.1.50` |
| Application | `YouTube`      |
| Domain      | `facebook.com` |

Once a flow is identified as blocked, all subsequent packets belonging to that connection are discarded.

---

# Core Components

| Component           | Responsibility                           |
| ------------------- | ---------------------------------------- |
| PCAP Reader         | Reads packets from PCAP files            |
| Packet Parser       | Parses Ethernet, IPv4, TCP and UDP       |
| SNI Extractor       | Extracts domains from TLS Client Hello   |
| HTTP Host Extractor | Extracts Host header                     |
| Rule Manager        | Maintains blocking rules                 |
| Connection Tracker  | Tracks active flows                      |
| Thread Safe Queue   | Synchronization between threads          |
| DPI Engine          | Coordinates complete processing pipeline |

---

# Building

## Single Threaded

```bash
g++ -std=c++17 -O2 -I include \
src/main_working.cpp \
src/pcap_reader.cpp \
src/packet_parser.cpp \
src/sni_extractor.cpp \
src/types.cpp \
-o dpi_simple
```

---

## Multi Threaded

```bash
g++ -std=c++17 -pthread -O2 -I include \
src/dpi_mt.cpp \
src/pcap_reader.cpp \
src/packet_parser.cpp \
src/sni_extractor.cpp \
src/types.cpp \
-o dpi_engine
```

---

# Running

Basic usage:

```bash
./dpi_engine input.pcap output.pcap
```

Block applications:

```bash
./dpi_engine input.pcap output.pcap \
--block-app YouTube \
--block-app TikTok
```

Block domains:

```bash
./dpi_engine input.pcap output.pcap \
--block-domain facebook
```

Block IPs:

```bash
./dpi_engine input.pcap output.pcap \
--block-ip 192.168.1.50
```

Configure threads:

```bash
./dpi_engine input.pcap output.pcap \
--lbs 4 \
--fps 4
```

---

# Sample Output

```
Total Packets : 77
TCP Packets   : 73
UDP Packets   : 4

Forwarded     : 69
Dropped       : 8

Detected Applications

HTTPS
YouTube
Facebook
Google
DNS
Unknown
```

The report also includes:

* Packet statistics
* Application breakdown
* Thread utilization
* Detected domains (SNI)
* Dropped packet count

---

# Technologies Used

* C++17
* Multithreading (`std::thread`)
* Mutexes
* Condition Variables
* Producer-Consumer Pattern
* Hash Maps
* PCAP File Format
* TLS Protocol
* HTTP Protocol

---

# Future Improvements

* QUIC / HTTP3 support
* Bandwidth throttling
* Live statistics dashboard
* Persistent rule storage
* IPv6 support
* Web-based visualization
* Machine Learning based application classification

---

# Learning Outcomes

This project demonstrates practical implementation of:

* Deep Packet Inspection (DPI)
* Network Protocol Parsing
* TLS Handshake Analysis
* Stateful Flow Tracking
* High-performance Multithreading
* Thread-safe Queues
* Producer-Consumer Architecture
* Hash-based Load Balancing
* Application Classification
* Packet Filtering

---

# License

This project is intended for educational and research purposes. Use responsibly and ensure compliance with applicable laws and organizational policies.
