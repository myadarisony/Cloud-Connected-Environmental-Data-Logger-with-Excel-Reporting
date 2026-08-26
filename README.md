# 🌡️ Smart Temperature & Smoke Monitoring System

## 📌 Project Overview

The **Smart Temperature & Smoke Monitoring System** is an ARM7-based embedded IoT project designed to monitor **temperature and smoke conditions in real time**.

The system is developed using an **LPC21xx/LPC2129 ARM7 microcontroller** and integrates an **LM35 temperature sensor**, **MQ2 smoke/gas sensor**, **RTC**, **16×2 LCD**, **matrix keypad**, **I2C EEPROM**, **ESP-01 Wi-Fi module**, **UART**, **I2C**, and **buzzer alert system**.

The measured temperature and smoke status are displayed locally on the LCD. The ESP-01 connects the system to the **ThingSpeak cloud platform**, where sensor information can be uploaded for remote monitoring. EEPROM/I2C support is included for storing configuration data, and an external interrupt is used for the user switch.

---

## 🎯 Objectives

| Objective | Description |
|---|---|
| 🌡️ Temperature Monitoring | Measure temperature using the LM35 sensor |
| 💨 Smoke Detection | Detect smoke/gas using the MQ2 sensor |
| ⏰ RTC Monitoring | Maintain and display real-time date and time |
| 🖥️ LCD Display | Display temperature, date, time, and smoke status |
| ☁️ Cloud Monitoring | Upload sensor information to ThingSpeak |
| 💾 EEPROM Storage | Provide non-volatile storage through I2C EEPROM |
| ⌨️ User Control | Configure system parameters through keypad/menu |
| 🚨 Alert System | Activate buzzers during abnormal conditions |
| 📡 UART Communication | Communicate with ESP-01 using UART |

---

## 🧰 Hardware Components

| Component | Purpose |
|---|---|
| LPC21xx / LPC2129 ARM7 Microcontroller | Main controller |
| LM35 Temperature Sensor | Measures temperature |
| MQ2 Smoke/Gas Sensor | Detects smoke/gas |
| 16×2 LCD | Displays system information |
| RTC | Maintains time and date |
| 4×4 Matrix Keypad | User menu and configuration |
| I2C EEPROM | Stores configuration/data |
| ESP-01 Wi-Fi Module | Provides Wi-Fi/cloud connectivity |
| Temperature Buzzer | Indicates high-temperature condition |
| Gas Buzzer | Indicates smoke/gas detection |
| Switch | Activates the configuration menu through external interrupt |

---

## 💻 Software & Technologies

- **Embedded C**
- **Keil µVision**
- **LPC21xx ARM7 / LPC2129**
- **ADC**
- **UART**
- **I2C**
- **RTC**
- **ESP-01 / ESP8266**
- **AT Commands**
- **ThingSpeak Cloud**
- **16×2 LCD interfacing**
- **4×4 keypad interfacing**
- **External interrupts**

---

## 🏗️ System Architecture

```text
                  ┌─────────────────┐
                  │   LM35 Sensor   │
                  └────────┬────────┘
                           │ Analog
                           ▼
                  ┌─────────────────┐
                  │       ADC       │
                  └────────┬────────┘
                           │
                           ▼
       ┌────────────────────────────────────┐
       │          LPC21xx / ARM7            │
       │          Main Controller           │
       └──────┬──────┬──────┬──────┬────────┘
              │      │      │      │
              ▼      ▼      ▼      ▼
            LCD     RTC   Keypad  UART
                                  │
                                  ▼
                              ESP-01
                                  │
                                  ▼
                             ThingSpeak

       MQ2 ─────────► GPIO ─────────► Gas Buzzer
                                  
       Temperature ───────────────► Temp Buzzer

       EEPROM ◄────────────── I2C ─────────────► LPC21xx
```

---

## 🔄 Working Principle

1. The LPC21xx initializes the LCD, UART, ADC, RTC, I2C, keypad, interrupt, and MQ2 interface.
2. The RTC is configured with the required date and time.
3. The LM35 produces an analog voltage proportional to temperature.
4. The LPC21xx ADC reads the LM35 signal and converts it into a digital value.
5. The digital ADC value is converted into sensor voltage and displayed as temperature.
6. RTC information is continuously read and displayed on the LCD.
7. The MQ2 sensor is monitored through a digital GPIO input.
8. The system compares the temperature with the configured setpoint.
9. When temperature exceeds the setpoint, the temperature buzzer is switched ON and the temperature value is uploaded to ThingSpeak.
10. When smoke is detected, the gas buzzer is switched ON and a smoke-status update is uploaded to ThingSpeak.
11. When smoke is cleared, the system uploads the cleared status.
12. The ESP-01 communicates with the LPC21xx through UART using AT commands.
13. The external interrupt detects the configuration switch and opens the keypad menu.
14. The system continuously repeats these operations for real-time monitoring.

---

## ⭐ Key Features

| Feature | Description |
|---|---|
| 🌡️ LM35 Monitoring | Real-time temperature measurement through ADC |
| 💨 MQ2 Detection | Digital smoke/gas status monitoring |
| ⏰ RTC | Real-time time and date monitoring |
| 🖥️ LCD | Local display of temperature and status |
| 🚨 Temperature Alert | Buzzer when temperature exceeds the setpoint |
| 🚨 Smoke Alert | Buzzer and LCD indication when smoke is detected |
| 💾 EEPROM | I2C-based non-volatile data storage support |
| 📡 ESP-01 | Wi-Fi connectivity |
| ☁️ ThingSpeak | Cloud-based sensor monitoring |
| ⌨️ Keypad Menu | User configuration interface |
| ⚡ External Interrupt | Switch event handling |
| 🔄 Continuous Monitoring | Real-time embedded monitoring loop |

---

## 🌡️ Temperature Monitoring

The LM35 is connected to an ADC channel of the LPC21xx.

```text
LM35
  ↓
Analog Voltage
  ↓
LPC21xx ADC
  ↓
10-bit ADC Result
  ↓
Voltage Calculation
  ↓
Temperature Calculation
  ↓
LCD Display / ThingSpeak
```

The source code uses a **3.3 V ADC reference** and a **10-bit ADC result**.

```text
Analog Voltage = ADC Value × 3.3 / 1023
```

The LM35 relationship is approximately:

```text
Temperature (°C) = Sensor Voltage × 100
```

---

## 💨 MQ2 Smoke Detection

The MQ2 sensor is configured as a digital GPIO input. The program tracks the current and previous smoke states so that ThingSpeak is updated when the smoke condition changes.

### Smoke Detected

```text
MQ2 Status = 0
      ↓
Smoke Detected
      ↓
Gas Buzzer ON
      ↓
LCD: Gas alert
      ↓
ThingSpeak Field 3 = 1
```

### Smoke Cleared

```text
MQ2 Status = 1
      ↓
Gas Cleared
      ↓
Gas Buzzer OFF
      ↓
LCD: Gas Cleared
      ↓
ThingSpeak Field 3 = 0
```

---

## 🚨 Temperature Alert

The temperature is continuously compared with the configured `set_point`.

```text
Measured Temperature
          ↓
Compare with Setpoint
          ↓
   ┌──────┴──────┐
   │             │
 Normal       Above Setpoint
   │             │
Buzzer OFF    Buzzer ON
                 │
                 ▼
        ThingSpeak Field 2
```

The current source initializes the temperature setpoint to **32°C**.

---

## 💾 EEPROM & I2C

The project contains an I2C driver and an EEPROM driver supporting:

- Single-byte write
- Single-byte read
- Page write
- Sequential read
- I2C START condition
- I2C STOP condition
- Repeated START
- ACK/NACK based data reception

The EEPROM interface uses slave address **0x50** as defined in the project headers.

---

## 📡 ESP-01 Wi-Fi Communication

The ESP-01 communicates with the LPC21xx through **UART0**. The project uses ESP8266 AT commands to configure the module and communicate with ThingSpeak.

### Important AT Commands

```text
AT
```
Checks whether the ESP-01 is responding.

```text
ATE0
```
Disables command echo.

```text
AT+CIPMUX=0
```
Configures single TCP connection mode.

```text
AT+CWQAP
```
Disconnects from the current access point.

```text
AT+CWJAP="SSID","PASSWORD"
```
Connects the ESP-01 to a Wi-Fi network.

```text
AT+CIPSTART="TCP","api.thingspeak.com",80
```
Creates a TCP connection with the ThingSpeak server.

```text
AT+CIPSEND=48
```
Requests the ESP-01 to prepare for the specified number of bytes.

```text
AT+CIPCLOSE
```
Closes the TCP connection.

---

## ☁️ ThingSpeak Cloud Upload

The project sends HTTP GET requests to ThingSpeak using the ESP-01.

The source code uses fields for different data types:

| Field | Data |
|---|---|
| Field 1 | Periodic temperature |
| Field 2 | High-temperature value |
| Field 3 | Smoke status |

The temperature is uploaded periodically. The source code uses a **3-minute interval** for the periodic temperature update.

The request format used by the project is similar to:

```text
GET /update?api_key=YOUR_API_KEY&field1=value
```

> **Security:** Replace the placeholder API key, SSID, and password with your own credentials. Do not upload real Wi-Fi passwords or API keys to a public GitHub repository.

---

## 📺 LCD Display

The 16×2 LCD is used for local monitoring and status messages such as:

- Connecting to ESP01
- ESP-01 response status
- Temperature
- RTC information
- Gas status
- Gas alert
- Gas cleared
- Data uploading
- Data updated / not updated

Example status messages include:

```text
Connecting
To ESP01
```

```text
Gas alert
```

```text
Gas Cleared
```

---

## ⌨️ Keypad & External Interrupt

The project uses a 4×4 matrix keypad with:

- Rows: `P1.16 – P1.19`
- Columns: `P1.20 – P1.23`

The external switch is handled using **EINT0**. When the switch interrupt occurs, the interrupt service routine sets a software flag. The main loop then opens the configuration menu.

```text
Switch Press
     ↓
EINT0 Interrupt
     ↓
interrupt_flag = 1
     ↓
Main Loop
     ↓
Menu / Keypad Configuration
```

---

## ⚡ UART Interrupt Handling

UART0 is used for communication with the ESP-01. The UART receive interrupt stores incoming ESP-01 responses in a receive buffer.

```text
ESP-01 Response
      ↓
UART0 RX
      ↓
UART Interrupt
      ↓
Receive Buffer
      ↓
Response Checking
```

The project uses a character buffer for ESP-01 responses and checks for responses such as `OK`, `CONNECT`, `ALREADY CONNECTED`, and `WIFI CONNECTED`.

---


## 🔌 Main Interfaces Used

| Interface | Purpose |
|---|---|
| ADC | LM35 temperature measurement |
| GPIO | MQ2 smoke input and buzzer control |
| RTC | Time and date management |
| I2C | EEPROM communication |
| UART0 | ESP-01 communication |
| LCD | Local monitoring |
| Matrix Keypad | User input |
| EINT0 | Switch interrupt |

```
## Project Output
<img width="1600" height="1200" alt="esp01 connection" src="https://github.com/user-attachments/assets/89c5cc54-b70f-4721-b975-9496e52d91e1" />
<img width="1600" height="1200" alt="keypad_interface" src="https://github.com/user-attachments/assets/0ca02e0e-8daa-4da9-8e94-612d59279b0d" />
---
## ThingsPeak output
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/1e6bc84f-c863-43de-8d1a-c8db04579784" />

---

## 🚀 Advantages

- Real-time temperature monitoring.
- Real-time smoke detection.
- Local LCD indication.
- Audible temperature and smoke alerts.
- Wi-Fi connectivity using ESP-01.
- Cloud monitoring through ThingSpeak.
- EEPROM/I2C support.
- UART interrupt-based ESP-01 response handling.
- External interrupt-based switch handling.
- Modular Embedded C source code.

---

## 🏭 Applications

- 🏭 Industrial environmental monitoring
- 🔬 Laboratory monitoring
- 📦 Warehouse monitoring
- 🏥 Environmental safety monitoring
- 🏠 Smart home monitoring
- 🌾 Agricultural environment monitoring
- 🚨 Temperature and smoke alert systems
- ☁️ IoT-based environmental monitoring

---

## 🔮 Future Enhancements

- Add a web dashboard for real-time monitoring.
- Add mobile notifications for smoke and temperature alerts.
- Store historical sensor readings in a database.
- Add additional environmental sensors.
- Add automatic email/SMS alerts.
- Replace HTTP with HTTPS for secure cloud communication.
- Add graphical temperature and smoke history.
- Add automatic report generation.

---

## 🧠 Skills Demonstrated

- Embedded C
- ARM7 / LPC21xx
- LPC2129
- ADC interfacing
- LM35 interfacing
- MQ2 smoke sensor interfacing
- RTC interfacing
- I2C communication
- EEPROM interfacing
- UART communication
- ESP-01 / ESP8266
- AT commands
- ThingSpeak IoT
- LCD interfacing
- Matrix keypad
- External interrupts
- Buzzer control
- Modular embedded firmware development

---

## 👩‍💻 Author

**Sony Myadari**

### 📌 Project Focus

**Embedded Systems | ARM7 | IoT | Sensor Monitoring | UART | I2C | ESP-01 | ThingSpeak**

---

## 📜 License

This project is created for educational and demonstration purposes.
