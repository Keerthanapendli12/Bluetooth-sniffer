# Bluetooth-sniffer
## week 1
## BLE vs Bluetooth Classic

Bluetooth technology consists of two main variants: **Bluetooth Classic (BR/EDR)** and **Bluetooth Low Energy (BLE)**. Although both operate in the **2.4 GHz ISM band**, they are designed for different applications.

### Bluetooth Classic
Bluetooth Classic is optimized for continuous, high-data-rate communication.

**Applications:**
- Wireless headphones and speakers
- File transfer
- Wireless keyboards and mice
- Car infotainment systems

**Features:**
- Higher data throughput
- Continuous connection
- Higher power consumption
- Suitable for audio streaming

---

### Bluetooth Low Energy (BLE)

BLE is designed for applications requiring low power consumption while transmitting small amounts of data.

**Applications:**
- BLE Beacons
- Smartwatches
- Fitness bands
- Heart rate monitors
- Smart sensors
- IoT devices

**Features:**
- Very low power consumption
- Long battery life
- Supports advertising without establishing a connection
- Ideal for IoT and wearable devices

---

## Advertising Channels

BLE uses **40 RF channels**, numbered **0 to 39**.

- **Advertising Channels:** 37, 38, 39
- **Data Channels:** 0–36

Advertising channels are used to:
- Broadcast advertisements
- Discover nearby BLE devices
- Establish BLE connections

A Bluetooth sniffer must monitor **all three advertising channels (37, 38, and 39)** to capture every advertisement packet.

---

## Advertisement Packets

BLE devices periodically broadcast **advertisement packets** before establishing a connection.

### ADV_IND (Connectable Undirected Advertisement)

- Allows any BLE scanner to discover the device.
- Supports connection requests.
- Supports Scan Requests and Scan Responses.
- Commonly used by smartphones, smartwatches, fitness bands, and BLE peripherals.

---

### ADV_NONCONN_IND (Non-Connectable Advertisement)

- Used only for broadcasting information.
- Does not allow BLE connections.
- Ignores Scan Requests.
- Commonly used by BLE beacons and asset tracking devices.

---

### SCAN_RSP (Scan Response)

A Scan Response is sent **only when a scanner performs Active Scanning**.

Communication flow:

```
Advertiser
      │
Advertisement Packet
      │
      ▼
Scanner
      │
Scan Request
      │
      ▼
Advertiser
      │
Scan Response
      │
      ▼
Scanner
```

A Scan Response may contain:
- Complete Device Name
- Manufacturer Specific Data
- Additional Service Data
- Extra Service UUIDs

---

## Bluetooth MAC Address Randomization

Every Bluetooth device has a **48-bit Bluetooth Device Address (BD_ADDR)**. To improve user privacy, modern BLE devices often use random addresses instead of their permanent MAC address.

### Public Address

- Permanently assigned by the manufacturer.
- Based on an IEEE Organizationally Unique Identifier (OUI).
- Globally unique.
- Remains constant throughout the device's lifetime.

**Advantage:** Easy device identification.

**Disadvantage:** Can be used to track the device.

---

### Random Static Address

- Randomly generated.
- Remains unchanged until the device is restarted or generates a new address.
- Does not use an IEEE OUI.

**Purpose:** Provides privacy while maintaining a stable address during operation.

---

### Random Private Resolvable Address (RPA)

- Changes periodically (typically every 15 minutes).
- Generated using an Identity Resolving Key (IRK).
- Can be resolved only by trusted paired devices.
- Prevents long-term tracking while allowing trusted devices to recognize each other.

---

### Random Private Non-Resolvable Address (NRPA)

- Randomly generated.
- Cannot be resolved to the original device.
- Usually changes frequently.
- Used for anonymous BLE advertising and maximum privacy.

---

## Comparison of BLE Address Types

| Address Type | Permanent | Changes Periodically | Can Be Resolved | Privacy |
|--------------|-----------|----------------------|-----------------|----------|
| Public | ✔ | ✘ | Not Required | Low |
| Random Static | ✘ | Rarely | ✘ | Medium |
| Random Private Resolvable (RPA) | ✘ | ✔ | ✔ (Trusted Devices Only) | High |
| Random Private Non-Resolvable (NRPA) | ✘ | ✔ | ✘ | Very High |

## wEEK - 2
Here's a clean, professional **GitHub README** section for these topics.

---

# Advertising Data (AD) Structure

BLE advertisement packets consist of one or more **Advertising Data (AD) Structures**. Each AD structure contains a specific type of information about the device.

Each AD structure follows the format:

```text
+--------+------+-------------+
| Length | Type |    Data     |
+--------+------+-------------+
```

* **Length** – Number of bytes in the AD structure (excluding the Length byte itself).
* **Type** – Identifies the type of information being advertised.
* **Data** – The actual information associated with the AD Type.

A single advertisement packet can contain **multiple AD structures**, each describing different information such as the device name, supported services, manufacturer data, and more.

---

## Common AD Types

### 0x01 – Flags

Indicates the BLE capabilities and discoverability of the device.

Common Flags:

* LE Limited Discoverable Mode
* LE General Discoverable Mode
* BR/EDR Not Supported (BLE Only)

Example:

```text
02 01 06
```

---

### 0x09 – Complete Local Name

Contains the complete device name that is displayed during scanning.

Example:

```text
MAX
Galaxy Watch
ESP32
```

---

### 0x08 – Shortened Local Name

Contains a shortened version of the device name when there is insufficient space for the complete name.

---

### 0x03 – Complete List of 16-bit Service UUIDs

Advertises all supported standard 16-bit BLE services.

Example:

```text
180D → Heart Rate Service
180F → Battery Service
```

---

### 0x16 – Service Data

Contains service-specific data.

Structure:

```text
Service UUID + Service Value
```

Used by:

* Eddystone Beacons
* IoT Devices
* Custom BLE Services

---

### 0xFF – Manufacturer Specific Data

Contains manufacturer-defined information.

Structure:

```text
Company ID + Manufacturer Data
```

Used by:

* Apple iBeacon
* Xiaomi Mi Band
* Samsung Devices
* Fitbit
* Other proprietary BLE devices

---

# BLE Beacon Formats

BLE beacons periodically broadcast information without requiring a connection.

---

## iBeacon (Apple)

Uses **Manufacturer Specific Data (AD Type 0xFF)**.

Structure:

* Apple Company ID
* UUID
* Major
* Minor
* TX Power

### Fields

* **UUID** – Identifies a group or organization.
* **Major** – Identifies a subgroup (e.g., building or floor).
* **Minor** – Identifies an individual beacon.
* **TX Power** – Reference transmit power used for distance estimation.

Applications:

* Indoor navigation
* Asset tracking
* Proximity marketing

---

## Eddystone (Google)

Uses **Service Data (AD Type 0x16)**.

Supports multiple frame types:

* **UID** – Unique beacon identifier.
* **URL** – Broadcasts a website URL.
* **TLM** – Telemetry data (battery voltage, temperature, uptime).
* **EID** – Ephemeral identifier for enhanced security.

Applications:

* Smart cities
* IoT deployments
* Physical web
* Asset tracking

---

## AltBeacon

An open-source beacon format designed as an alternative to iBeacon.

Uses **Manufacturer Specific Data (0xFF)** and contains:

* Beacon Identifier
* Manufacturer Data
* TX Power

Applications:

* Cross-platform beacon deployments
* Custom BLE beacon solutions

---

## Beacon Format Comparison

| Feature       | iBeacon            | Eddystone          | AltBeacon       |
| ------------- | ------------------ | ------------------ | --------------- |
| Developer     | Apple              | Google             | Radius Networks |
| AD Type       | 0xFF               | 0x16               | 0xFF            |
| Identifier    | UUID, Major, Minor | UID, URL, TLM, EID | Beacon ID       |
| Open Standard | No                 | Yes                | Yes             |

---

# Active vs Passive Scanning

BLE devices can be discovered using two different scanning methods.

---

## Passive Scanning

In passive scanning, the scanner **only listens** to advertisement packets.

### Features

* Does not transmit any packets.
* Receives only advertisement packets.
* Lower power consumption.
* Cannot receive Scan Response packets.
* Suitable for monitoring nearby BLE devices without interacting with them.

Communication:

```text
Advertiser
     │
Advertisement Packet
     │
     ▼
Scanner
```

---

## Active Scanning

In active scanning, the scanner listens for advertisement packets and then sends a **Scan Request** to obtain additional information.

### Features

* Receives advertisement packets.
* Sends a Scan Request.
* Receives a Scan Response if supported by the advertiser.
* Provides more detailed device information.
* Slightly higher power consumption.
* Reveals the presence of the scanner because it transmits Scan Requests.

Communication:

```text
Advertiser
      │
Advertisement Packet
      │
      ▼
Scanner
      │
Scan Request
      │
      ▼
Advertiser
      │
Scan Response
      │
      ▼
Scanner
```

---

## Passive vs Active Scanning Comparison

| Feature                                  | Passive Scan | Active Scan |
| ---------------------------------------- | ------------ | ----------- |
| Listens to Advertisement Packets         | ✔            | ✔           |
| Sends Scan Request                       | ✘            | ✔           |
| Receives Scan Response                   | ✘            | ✔           |
| Can Obtain Additional Device Information | ✘            | ✔           |
| Reveals Scanner Presence                 | ✘            | ✔           |
| Power Consumption                        | Lower        | Higher      |


# week - 3
## Connection Sniffing

After two BLE devices establish a connection, they stop communicating on the advertising channels (37, 38, 39) and begin exchanging data on the **37 data channels (0–36)**.

To improve reliability and reduce interference, BLE devices use **Adaptive Frequency Hopping (AFH)**. Instead of staying on a single channel, both devices hop through the data channels in a **pseudo-random sequence**.

A Bluetooth sniffer must synchronize with this hopping sequence to capture all packets exchanged between the connected devices.

## Parameters Required for Connection Sniffing

### Access Address
- A unique **32-bit identifier** assigned to every BLE connection.
- Generated by the **Central** during connection establishment.
- Sent in the **CONNECT_IND/CONNECT_REQ** packet.
- Identifies packets belonging to a specific BLE connection.
- Remains constant throughout the connection.
- Usually changes when a new connection is established.

### CRC Init
- A **24-bit initialization value** used as the starting value for CRC calculation.
- Generated by the Central.
- Sent only once in the **CONNECT_IND/CONNECT_REQ** packet.
- Remains constant during the connection.
- Required by the receiver and Bluetooth sniffer to correctly verify packet integrity.

### Connection Interval (often called "Hop Interval")
- Specifies the time between consecutive connection events.
- Chosen by the Central during connection establishment.
- Sent in the **CONNECT_IND/CONNECT_REQ** packet.
- Determines **when** the devices exchange data and hop to the next data channel.
- Typical range: **7.5 ms to 4 seconds**.

### Hop Increment
- Determines **which data channel** will be used next.
- Generated by the Central.
- Sent during connection establishment.
- Remains constant throughout the connection.
- Works together with the Channel Map to generate the channel hopping sequence.

### Channel Map
- Indicates which of the 37 data channels are currently usable.
- Helps devices avoid channels experiencing interference.
- Can be updated during an active connection using the **LL_CHANNEL_MAP_IND** control packet.
- The hopping algorithm remains the same, but disabled channels are skipped.

---

# Host Controller Interface (HCI)

The **Host Controller Interface (HCI)** is the standard communication interface between the Bluetooth **Host** and the Bluetooth **Controller**.

## Host

The Host is responsible for higher Bluetooth protocol layers:

- Application
- GATT
- ATT
- SMP
- L2CAP

## Controller

The Controller manages the lower Bluetooth layers:

- Link Layer
- Physical Layer (Radio)

## Functions of HCI

- Transfers commands from the Host to the Controller.
- Transfers events from the Controller to the Host.
- Transfers Bluetooth data between the Host and Controller.
- Configures Bluetooth controller parameters such as scanning, advertising, and connections.

## HCI Packet Types

### HCI Command
Host → Controller

Examples:
- Start Scan
- Stop Scan
- Connect
- Disconnect
- Start Advertising

### HCI Event
Controller → Host

Examples:
- Advertisement Received
- Device Found
- Connection Complete
- Disconnection Complete

### HCI Data
Host ↔ Controller

Transfers Bluetooth data between the Host and Controller after a connection is established.

## ESP32 as an HCI Dongle

Normally, the ESP32 runs both the Bluetooth Host and Controller.

When flashed with **HCI firmware**, the ESP32 disables its Host and behaves only as a Bluetooth Controller.

A PC running **Wireshark** or **BlueZ** acts as the Bluetooth Host and communicates with the ESP32 using HCI commands over **USB/UART**.

This allows the ESP32 to function as a Bluetooth **HCI dongle** for packet capture and analysis.

---

# BLE Packet Structure

Every BLE packet transmitted over the air follows this structure:

```
+----------+----------------+-------------+---------+
| Preamble | Access Address |     PDU     |   CRC   |
+----------+----------------+-------------+---------+
```

## Preamble

- First field of every BLE packet.
- Synchronizes the transmitter and receiver.
- Helps the receiver detect the start of the packet.
- Size:
  - 1 byte (1M PHY)
  - 2 bytes (2M PHY)

---

## Access Address

- 32-bit identifier.
- Used to identify a BLE connection.

### Advertising Packets

Always use the fixed Access Address:

```
0x8E89BED6
```

### Data Packets

Use a randomly generated Access Address assigned during connection establishment.

---

## Protocol Data Unit (PDU)

The PDU contains the actual Bluetooth information.

It consists of:

- PDU Header
- Payload

The payload depends on whether the packet is an **Advertising PDU** or a **Data PDU**.

---

## CRC (Cyclic Redundancy Check)

- Used for error detection.
- Size: **24 bits (3 bytes)**.
- Calculated using the packet contents and CRC Init.
- Ensures packet integrity.

---

# Advertising PDU vs Data PDU

## Advertising PDU

Used before a BLE connection is established.

### Purpose

- Device discovery.
- Advertising.
- Connection establishment.
- Beacon broadcasting.

### Channels Used

Advertising Channels:

- 37
- 38
- 39

### Contains

- PDU Header
- Advertiser Address
- Advertising Data

Advertising Data may include:

- Device Name
- Flags
- Service UUIDs
- Manufacturer Specific Data
- Appearance
- TX Power

### Common Advertising PDUs

- ADV_IND
- ADV_NONCONN_IND
- ADV_SCAN_IND
- ADV_DIRECT_IND
- SCAN_REQ
- SCAN_RSP
- CONNECT_IND (CONNECT_REQ)

---

## Data PDU

Used after a BLE connection has been established.

### Purpose

Transfers data between connected devices.

### Channels Used

Data Channels:

- 0–36

### Contains

- Data PDU Header
- Payload

Payload may contain:

- L2CAP packets
- ATT packets
- GATT data
- SMP messages
- Application data
- Link Layer Control messages

---

## Advertising PDU vs Data PDU

| Feature | Advertising PDU | Data PDU |
|----------|-----------------|-----------|
| Used Before Connection | ✔ | ✘ |
| Used After Connection | ✘ | ✔ |
| Channels | 37–39 | 0–36 |
| Access Address | Fixed (0x8E89BED6) | Connection-specific |
| Contains Advertiser Address | ✔ | ✘ |
| Contains Advertising Data | ✔ | ✘ |
| Contains ATT/GATT Data | ✘ | ✔ |
| Contains SMP Data | ✘ | ✔ |
| Supports Encryption | ✘ | ✔ |

---

# Link Layer ID (LLID)

The **LLID (Link Layer Identifier)** is a **2-bit field** present in the **Data PDU Header**. It tells the receiver what kind of payload is carried inside the Data PDU.

## Functions

- Identifies the type of payload carried in the packet.
- Helps the receiver determine how to process the payload.
- Indicates whether the payload contains user data, Link Layer control information, or no payload.

## LLID Values

| LLID | Meaning |
|-------|---------|
| `01` | Continuation or fragment of an L2CAP packet |
| `10` | Start of an L2CAP packet or a complete L2CAP packet |
| `11` | Link Layer Control PDU |
| `00` | Reserved (not used for valid data packets) |

## Examples

### LLID = 10

The payload contains the beginning (or entire) L2CAP packet.

Example:

```
ATT Read Request
Heart Rate Value
Temperature Data
Battery Level
```

### LLID = 01

The payload is a continuation fragment of a larger L2CAP packet.

### LLID = 11

The payload contains a Link Layer Control message such as:

- Connection Parameter Update
- Channel Map Update
- PHY Update
- Feature Exchange
- Version Exchange
- Encryption Request
- Encryption Response

The receiving device uses the LLID to determine whether the payload should be forwarded to **L2CAP** or processed directly by the **Link Layer**.
