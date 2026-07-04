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

