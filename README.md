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
