
#  IoT Real-Time Alert System (MQTT + Telegram)

---

##  Objective

This lab extends the IoT communication pipeline by adding a **real-time alert system**.

When the temperature exceeds a defined threshold, the system sends a **Telegram notification**.

This simulates a **cloud-based monitoring and alert service** used in real-world IoT applications.

---

##  System Architecture

```text
Sensor (Laptop 1)
      │
      │ Socket
      ▼
Edge Device (Laptop 2)
      │
      │ MQTT Publish
      ▼
MQTT Broker
      │
      │ MQTT Subscribe
      ▼
Cloud Server (Laptop 1)
      │
      ▼
Telegram Alert
```

---

##  Data Flow

* Sensor generates temperature data
* Edge device receives data via **Socket**
* Edge device forwards data via **MQTT**
* Cloud subscriber monitors data
* Telegram alert is triggered when threshold is exceeded

---

#  Step 1 — I Created Telegram Bot
1. Search for **BotFather**
2. Start chat and type:

```text
/newbot
```

3. Following instructions and  I got my **Bot Token**

```text
8379835119:AAGOQaOfUxVY-ADgelPFwkUvNTIOKM5S3PE
```

 Save the token.

---

#  Step 2 — Got my Chat ID

1. Send a message to your bot
2. Open in browser:

```text
https://api.telegram.org/botYOUR_TOKEN/getUpdates
```

3. Find my Chat ID:

```json
"chat":{"id":268528820}
```
---

#  Step 3 — Install Requirements

Install required libraries:

```bash
pip install paho-mqtt requests
```

Libraries used:

* Eclipse Paho MQTT
* Requests

---

#  Step 4 — MQTT Alert Subscriber

**File:** `mqtt_alert_subscriber.py`

```python
import paho.mqtt.client as mqtt
import requests

broker = "broker.emqx.io"
topic = "savonia/iot/temperature"

TOKEN = "YOUR_TELEGRAM_TOKEN"
CHAT_ID = "YOUR_CHAT_ID"

threshold = 28

def send_telegram(message):
    url = f"https://api.telegram.org/bot{TOKEN}/sendMessage"

    payload = {
        "chat_id": CHAT_ID,
        "text": message
    }

    requests.post(url, data=payload)

def on_message(client, userdata, msg):
    temperature = float(msg.payload.decode())
    print("Temperature:", temperature)

    if temperature > threshold:
        alert = f"ALERT: High temperature {temperature} °C"
        print(alert)
        send_telegram(alert)

client = mqtt.Client()
client.connect(broker,1883)

client.subscribe(topic)
client.on_message = on_message

client.loop_forever()
```

---

#  Step 5 — Run the System

---

### 1️ Cloud Alert Subscriber (Laptop 1)

```bash
python mqtt_alert_subscriber.py
```

---

### 2️ Edge Device (Laptop 2)

```bash
python edge_device.py
```

---

### 3️ Sensor Node (Laptop 1)

```bash
python socket_sensor.py
```

---

#  Output

###  Terminal Output

```text
Temperature: 29.3
ALERT: High temperature 29.3 °C
```

---

###  Telegram Message

```text
ALERT: High temperature 29.3 °C
```

---

#  Key Features

* Real-time monitoring
* Automatic alert triggering
* MQTT-based cloud communication
* Integration with messaging platform


#  Conclusion

This lab demonstrates how IoT systems can be extended with real-time alert mechanisms. 
By integrating MQTT communication with Telegram notifications, the system can automatically detect
abnormal conditions and notify users instantly. This approach is widely used in smart monitoring systems
such as environmental sensing, industrial automation, and healthcare applications.

