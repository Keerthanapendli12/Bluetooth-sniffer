
A Bluetooth Sniffer is a tool that captures and analyzes Bluetooth packets exchanged between BLE devices. It allows developers to observe Bluetooth communication without modifying the transmitting or receiving devices.Unlike a Bluetooth device that actively participates in communication, a sniffer passively listens to nearby Bluetooth transmissions and records the exchanged packets for analysis.
## WEEK-1
## BLE vs Bluetooth Classic
Bluetooth technology is divided into Bluetooth Classic (BR/EDR) and Bluetooth Low Energy (BLE). Although both operate in the 2.4 GHz ISM band, they are designed for different applications.

## Bluetooth Classic (BR/EDR)
Bluetooth Classic is designed for applications that require continuous data transmission and relatively high data rates.
#### Features
- Higher data throughput than BLE.
- Continuous connection between devices.
- Higher power consumption.
- Suitable for streaming audio and transferring large amounts of data.
#### Common Applications
Wireless headphones and speakers, File transfer, Wireless keyboards and mice, Car infotainment systems, Gaming controllers

## Bluetooth Low Energy (BLE)
BLE is designed for applications requiring low power consumption while transmitting small amounts of data.
#### Features
* Very low power consumption.
* Small packet sizes.
* Supports advertising without establishing a connection.
* Long battery life (months or years).
* Ideal for IoT and wearable devices.
#### Common Applications
Fitness bands, Smartwatches, BLE Beacons ,Heart rate monitors, Temperature sensors, Smart locks, IoT devices

## BLE Advertising Channels
- BLE uses 40 RF channels, numbered 0–39.
- Advertising Channels: 37, 38, 39
- Data Channels: 0–36
- Data Channels
0 ------------------------ 36

Advertising Channels:
37       38       39
### Purpose of Advertising Channels
Advertising channels are used for:
- Device discovery
- Broadcasting information
- Connection establishment
- Beacon transmission
These three channels are placed to minimize interference from Wi-Fi operating in the 2.4 GHz band.

#### Why only 3 Advertising Channels?
Using only three dedicated channels:
Reduces power consumption.
Increases the probability that nearby scanners discover the device.
Minimizes Wi-Fi interference.

## BLE Sniffing
A BLE sniffer must monitor the advertising channels:
- Channel 37
- Channel 38
- Channel 39
to capture all advertisement packets.

### BLE Advertisement Packets
Advertising packets are transmitted before a BLE connection is established.
Different advertisement packet types are used depending on the device's purpose.
### ADV_IND (Connectable Undirected Advertisement)
#### Features
- Most commonly used advertisement type.
- Allows any scanner to discover the device.
- Allows devices to initiate a connection.
- Supports Scan Requests.
#### Used By
Smartphones,
Smartwatches,
Fitness bands,
BLE peripherals.

### ADV_NONCONN_IND (Non-Connectable Advertisement)
#### Features
- Cannot establish a BLE connection.
- Used only for broadcasting information.
- Scan Requests are ignored.
#### Used By
- BLE Beacons
- Asset tracking devices
- Digital signage
- Location broadcasting
  
### SCAN_RSP (Scan Response)
A Scan Response is not an advertisement packet transmitted periodically.
It is sent only after a scanner performs Active Scanning.
#### Communication sequence:
Advertiser -
Advertisement Packet ->
Scanner ->
Scan Request ->
Advertiser ->
Scan Response ->
Scanner
#### Purpose
Provides additional information that could not fit into the Advertisement Packet.
Examples:
- Complete Device Name
- Manufacturer Specific Data
- Additional Service Data
- Extra Service UUIDs
  
## Bluetooth MAC Address Randomization
Every Bluetooth device uses a 48-bit Bluetooth Device Address (BD_ADDR).
To improve user privacy, modern BLE devices often use random addresses instead of their permanent MAC address.

### Public Address
#### Features
- Factory programmed.
- Assigned using an IEEE Organizationally Unique Identifier (OUI).
- Globally unique.
- Usually remains constant.
Example:
A4:CF:12:34:56:78
#### Advantages
- Permanent device identification.
- Easy device recognition.
#### Disadvantage
- Allows long-term device tracking.
  
### Random Static Address
#### Features
- Randomly generated.
- Remains unchanged until the device restarts or regenerates it.
- Does not contain an IEEE OUI.
Example:
D2:7A:9F:12:45:AB
#### Advantages
- Provides privacy.
- Appears as a consistent device during one operating session.
  
### Random Private Resolvable Address (RPA)
#### Features
- Changes periodically (typically every 15 minutes).
- Generated using an Identity Resolving Key (IRK).
- Trusted devices can resolve it to the original device identity.
- Other devices cannot identify or track it.
#### Advantages
- Prevents long-term tracking.
- Maintains privacy.
- Allows previously paired devices to recognize each other.
##### Modern smartphones commonly use RPAs.

### Random Private Non-Resolvable Address (NRPA)
#### Features
- Randomly generated.
- Cannot be resolved to the original device.
Usually changes frequently.
- Intended for anonymous advertising.
#### Advantages
- Maximum privacy.
- Impossible to identify the device using the address alone.
#### Common Applications
- Temporary BLE advertisements
- Anonymous BLE beacons
- Privacy-sensitive applications
- Comparison of BLE Address Types
  
##### Why is MAC Address Randomization Important?
Without address randomization, anyone with a BLE scanner could continuously identify and track the same device by its permanent MAC address. Randomization changes the advertised address periodically, making long-term tracking much more difficult while still allowing trusted paired devices to recognize each other (in the case of RPAs).

## WEEK - 2

## Advertising Data (AD) Structure

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

## BLE Beacon Formats

BLE beacons periodically broadcast information without requiring a connection.

---

### iBeacon (Apple)

Uses **Manufacturer Specific Data (AD Type 0xFF)**.

Structure:

* Apple Company ID
* UUID
* Major
* Minor
* TX Power

#### Fields

* **UUID** – Identifies a group or organization.
* **Major** – Identifies a subgroup (e.g., building or floor).
* **Minor** – Identifies an individual beacon.
* **TX Power** – Reference transmit power used for distance estimation.

Applications:

* Indoor navigation
* Asset tracking
* Proximity marketing

---

### Eddystone (Google)

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

### AltBeacon

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

## Active vs Passive Scanning

BLE devices can be discovered using two different scanning methods.

---

### Passive Scanning

In passive scanning, the scanner **only listens** to advertisement packets.

#### Features

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

### Active Scanning

In active scanning, the scanner listens for advertisement packets and then sends a **Scan Request** to obtain additional information.

#### Features

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

### Passive vs Active Scanning Comparison

| Feature                                  | Passive Scan | Active Scan |
| ---------------------------------------- | ------------ | ----------- |
| Listens to Advertisement Packets         | ✔            | ✔           |
| Sends Scan Request                       | ✘            | ✔           |
| Receives Scan Response                   | ✘            | ✔           |
| Can Obtain Additional Device Information | ✘            | ✔           |
| Reveals Scanner Presence                 | ✘            | ✔           |
| Power Consumption                        | Lower        | Higher      |

