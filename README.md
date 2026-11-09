# MikroTik RouterOS SOHO Network Setup Guide

This guide provides step-by-step instructions for setting up a complete **Basic Home / Small Office Network (SOHO)** using **MikroTik RouterOS** and **WinBox**. Following these steps will configure your WAN connection (ISP), LAN Bridge, DHCP Server, and NAT (Internet sharing).

## 📌 Project Overview & Specifications

* **Router Management Tool:** WinBox
* **ISP Connection (WAN):** `ether1` (DHCP Client / PPPoE Connection)
* **Local Network (LAN):** `ether2` to `ether5` (Bridged together)
* **Bridge Name:** `bridge-lan`
* **LAN IP Range:** `192.168.88.1/24`
* **DHCP IP Pool:** `192.168.88.10` – `192.168.88.254`
* **DNS Servers:** `8.8.8.8`, `8.8.4.4` (Google Public DNS)

---

## 🚀 Configuration Steps

### Step 1: Initial Preparation & Reset Configuration

1. Open **WinBox** and scan under the **Neighbors** tab.
2. Click on the router's **MAC Address**.
3. Log in using `Username: admin` and leave the `Password` field blank, then click **Connect**.
4. Navigate to **System** -> **Reset Configuration**.
5. Check **No Default Configuration** and click **Reset Configuration**.
6. Wait for the router to reboot, then log back in.

---

### Step 2: Create LAN Bridge & Add Interfaces

*This bridges ports `ether2` through `ether5` so they function together as a single network switch.*

1. Go to **Bridge** from the left-hand menu.
2. Under the **Bridge** tab, click **+ (Add New)**. Set `Name: bridge-lan` and click **OK**.
3. Switch to the **Ports** tab and click **+ (Add New)**.
4. Select `Interface: ether2` and `Bridge: bridge-lan`, then click **OK**.
5. Repeat Step 4 to add `ether3`, `ether4`, and `ether5` to `bridge-lan`.

---

### Step 3: Assign Local IP Address

1. Go to **IP** -> **Addresses**.
2. Click **+ (Add New)**.
3. Set `Address`: `192.168.88.1/24`.
4. Set `Interface`: `bridge-lan`.
5. Click **Apply** and then **OK**.

---

### Step 4: Configure DHCP Server (Automatic IP Assignment)

1. Go to **IP** -> **DHCP Server**.
2. Click on **DHCP Setup**.
3. **DHCP Server Interface:** Select `bridge-lan` ➡️ *Next*
4. **DHCP Address Space:** `192.168.88.0/24` ➡️ *Next*
5. **Gateway for DHCP Network:** `192.168.88.1` ➡️ *Next*
6. **Addresses to Give Out:** `192.168.88.10-192.168.88.254` ➡️ *Next*
7. **DNS Servers:** `8.8.8.8`, `8.8.4.4` ➡️ *Next*
8. **Lease Time:** `08:00:00` ➡️ *Next* and finish the wizard.

---

### Step 5: ISP WAN Connection Setup

#### Option A: Dynamic IP / DHCP Client (Auto-detect ISP)

1. Go to **IP** -> **DHCP Client**.
2. Click **+ (Add New)**.
3. Set `Interface: ether1`.
4. Ensure **Use Peer DNS** and **Use Peer NTP** are checked.
5. Click **OK**. *(Status should change to `bound`)*

#### Option B: PPPoE Client Setup (Username & Password based)

1. Go to **PPP** -> **Interface** tab -> Click **+ (Add New)** -> Select **PPPoE Client**.
2. **General Tab:** Set `Interface: ether1`.
3. **Dial Out Tab:**
   * Enter your ISP-provided **User** and **Password**.
   * Check **Add Default Route** and **Use Peer DNS**.
4. Click **Apply** and **OK**. *(Status flag **R** will appear once connected)*

---

### Step 6: Configure NAT (Masquerade for Internet Sharing)

1. Go to **IP** -> **Firewall** and click on the **NAT** tab.
2. Click **+ (Add New)**.
3. **General Tab:**
   * `Chain`: `srcnat`
   * `Out. Interface`: `ether1` *(or `pppoe-out1` if using PPPoE)*
4. **Action Tab:**
   * `Action`: `masquerade`
5. Click **Apply** and **OK**.

---

## ✅ Verification & Testing

1. **Router Internet Test:** Open **New Terminal** in WinBox and run:
   ```bash
   ping google.com
   ```
   *If responses are returned, the router successfully has internet access.*

2. **Client PC Internet Test:**
   * Reconnect your Ethernet cable on your computer.
   * Open Terminal/Command Prompt on your computer and verify your IP address (`192.168.88.X`).
   * Test browsing any webpage or ping `8.8.8.8`.

---

## 🔒 Recommended Post-Setup Security Measures

* [ ] Change the default `admin` password (**System** -> **Users**).
* [ ] Disable unused network services like Telnet, FTP, and WWW (**IP** -> **Services**).
* [ ] Keep RouterOS updated (**System** -> **Packages** -> **Check For Updates**).
