# Assignment: Internet of Things (IoT)

### 1) Project Links
- **Live Dashboard URL:** Render - [https://assignment-iot.onrender.com/ui](https://assignment-iot.onrender.com/ui)

Note: The Render free-tier deployment may require up to one minute to wake from inactivity.
- **Wokwi Simulation URL:** [Public Wokwi project link](https://wokwi.com/projects/463906891539102721)
- **Backend/Database URL:** [https://assignment-iot.onrender.com](https://assignment-iot.onrender.com)
- **Repository URL:** [GitHub](https://github.com/lenapear/assignment-iot)

### 2) Project Overview

#### Dashboard Interface

The dashboard displays live sensor values, historical chart visualisation, and LED control buttons for interacting with the simulated device.

![Dashboard Screenshot](images/dashboard.png)

#### Note About Historical Data

The dashboard queries and visualizes the most recent 30 minutes of sensor data from InfluxDB.

If no simulator data has been published within the last 30 minutes, the historical charts may initially appear empty. To generate new historical data, start the Wokwi simulation and allow sensor values to publish for a short period of time.

Live dashboard updates and historical visualization will then begin automatically.

### 3) Architecture and Data Flow

![](images/flowchart.png)

The system follows a publish-subscribe architecture using MQTT as the communication protocol.

The Wokwi simulated device publishes sensor data (temperature and humidity) to a public MQTT broker. Node-RED subscribes to this topic, processes incoming messages, and stores the data in InfluxDB for persistence.

The dashboard displays real-time MQTT updates while historical sensor data is retrieved from InfluxDB using query nodes.

Dashboard interactions publish MQTT control messages back to the Wokwi device, allowing LED control.

#### System Demonstration

The image below demonstrates the communication between the Wokwi simulator and the deployed dashboard. Sensor data is published in real time while dashboard commands control the LED state on the simulated ESP32 device.

![System Demonstration](images/system-demo.png)

### 4) Database Strategy
The system uses InfluxDB Cloud as a time-series database for storing sensor data.

#### Data Model
**Measurement:** environment
**Fields:**
- temperature (°C)
- humidity (%)
**Timestamp:** automatically generated or derived from incoming data

InfluxDB was chosen because it is optimized for continuous time-series data. It allows efficient storage and querying of sensor data over time.

The system queries the last 30 minutes of data for historical visualization. A retention policy of 30 days ensures old data is automatically removed to manage storage usage.

### 5) MQTT Topics and Payload Documentation

#### MQTT Broker - broker.hivemq.com
#### Sensor Data (Wokwi → MQTT Broker)
**Topic:** lnu/iot/ll224ve/sensor
**Payload:**
```json
{
  "temperature": 22,
  "humidity": 55,
  "timestamp": 1710063386
}
```
#### LED Control (Dashboard → Device)
**Topic:** lnu/iot/ll224ve/command/led
**Payload:**
```json
{
  "state": true
}
```

The sensor topic is used by the Wokwi device to publish environmental data.

The command topic is used by the dashboard to send control messages to the device, enabling bi-directional communication. There is a LED OFF button that sends the payload with the state "false" back to turn it off.


### 6) Reflection
Node-RED was used as both the backend and dashboard layer due to its strong integration with MQTT and InfluxDB, making it suitable for rapid IoT prototyping.

Real-time MQTT communication differs from traditional REST APIs because data is pushed continuously rather than requested on demand. This allows near-instant updates in the dashboard without manual refresh.

The most challenging part of the project was integrating all components (Wokwi device, MQTT broker, Node-RED, and InfluxDB). Ensuring consistent topic structure and correct data formatting was essential to achieve reliable end-to-end communication.
