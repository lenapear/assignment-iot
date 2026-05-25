# Assignment: Internet of Things (IoT)

### 1) Project Links
- **Live Dashboard URL:** [Link to deployed frontend, e.g. Vercel/Netlify/Cumulus]
- **Wokwi Simulation URL:** [Public Wokwi project link]
- **Backend/Database URL:** [Link to deployed backend stack, if applicable]
- **Repository URL:** [Link to your source code]

### 2) Project Overview
This project implements a complete Internet of Things (IoT) system using a simulated ESP32 device in Wokwi. The device reads environmental data from a DHT22 sensor, including temperature and humidity, and publishes this data to an MQTT broker at regular intervals.

Node-RED is used as the central processing layer. It subscribes to the MQTT sensor data, stores it in an InfluxDB time-series database, and provides both real-time and historical visualisation through a dashboard interface.

The dashboard also allows users to send control commands back to the simulated device, enabling LED control through MQTT. This demonstrates a full bi-directional IoT communication pipeline.

### 3) Architecture and Data Flow

[TO DO: Add Mermaid FlowChart Screenshot]()

The system follows a publish-subscribe architecture using MQTT as the communication protocol.

The Wokwi simulated device publishes sensor data (temperature and humidity) to a public MQTT broker. Node-RED subscribes to this topic, processes incoming messages, and stores the data in InfluxDB for persistence.

The dashboard retrieves real-time updates directly from MQTT, while historical data is fetched from InfluxDB using query nodes.

User interactions on the dashboard generate MQTT messages that are sent back through the broker to the Wokwi device, allowing control of an LED actuator.

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
