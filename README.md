# MFET 231: Integrated Condition Monitoring System

---

## Hardware Components
* **Computing:** 1 x Raspberry Pi 4 Model B 4GB
* **Sensors:** 1 x Adafruit SHT45 (Temperature & Humidity) and 1 x Adafruit TSL2561 (Luminosity Sensor) (I2C interface)
* **Prototyping:** 1 x Breadboard & Jumper Wire set
* **Software:**
   * **Database:** MySQL Server (`Final_SV231`)
   * **IDE:** VS Code
   * **HMI/SCADA:** Ignition Gateway

---

## Physical Setup Procedures
### Wiring & Integration
* **I2C Parallel Configuration:** Both sensors are wired in parallel on the breadboard to communicate over the same I2C bus.
    * **SDA (Data):** Connected to GPIO 2 (Pin 3).
    * **SCL (Clock):** Connected to GPIO 3 (Pin 5).
* **Power Distribution:** Sensors are connected to the 3.3V power rail and common Ground (GND) on the Raspberry Pi.
* **Hardware Verification:** Run `sudo i2cdetect -y 1` in the terminal to verify that both sensor hex addresses are visible on the bus.

---

## Digital & Software Procedures

### Phase A: Data Acquisition (Python Layer)
1. **Environment Setup:** Install Python 3 and required libraries: `mysql-connector-python`, `opcua`, and sensor drivers via `pip`.
2. **Python Logic (VS Code):**
   * **Polling:** The script initializes the I2C bus and polls sensors at a defined sampling rate.
   * **Variables:** Data is assigned to local Python variables (Temp, Hum, Lux).
   * **Local Storage:** The script establishes a connection to the MySQL database `Final_DB231` and executes `INSERT` queries to log timestamps and values.

### Phase B: Communication (OPC-UA Conduit)
1. **Server Configuration:** A Python-based **OPC-UA Server** is hosted on the Raspberry Pi (Port 4840).
2. **Data Mapping:**
   * Sensor variables are mapped to OPC-UA nodes. This allows external clients (Ignition) to browse and "subscribe" to real-time data streams.

### Phase C: Visualization & Control (Ignition Layer)
1. **Gateway Configuration:**
   * **Database Connection:** Linked Ignition to the MySQL Server `Final_SV231` to access the `Final_DB231` table.
   * **OPC Connection:** Added the Raspberry Pi as a device via the OPC-UA endpoint (`opc.tcp://[Pi_IP]:4840`).
2. **Designer Development:**
   * **Tag Management:** Tags were created by dragging nodes from the OPC Browser and linking them to the historical database.
   * **HMI Dashboard:**
     * **Real-time Gauges:** Visualizing current sensor states.
     * **Historical Trends:** 3 Charts showing data logged from the database.
     * **Alarm System:** Configured "High/Low" limits with a required manual "Acknowledge" button for human intervention.

---

## Data Architecture
| Component | Name/Path |
| :--- | :--- |
| **Database** | `Final_DB231` |
| **Server** | `Final_SV231` |
| **OPC Endpoint** | `opc.tcp://<Pi_IP>:4840` |
