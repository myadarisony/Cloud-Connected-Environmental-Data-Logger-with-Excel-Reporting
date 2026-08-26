# Smart Temperature & Smoke Monitoring System

## 📌 Project Description

The **Smart Temperature & Smoke Monitoring System** is an embedded real-time monitoring and alert system developed using an **LPC21xx ARM7 microcontroller**. The system continuously monitors temperature using an **LM35 sensor** and detects smoke/gas using an **MQ-2 sensor**. The measured temperature, smoke status, date, and time are displayed on a **16×2 LCD**.

The system uses an **ESP-01 (ESP8266) Wi-Fi module** to upload sensor information and alert events to the **ThingSpeak cloud platform**. A keypad is provided to configure the RTC and temperature threshold, while EEPROM is used to store the configured threshold. Buzzer alerts are generated when abnormal temperature or smoke conditions are detected.

## 🎯 Objective

- Monitor temperature in real time using the LM35 sensor.
- Detect smoke/gas using the MQ-2 sensor.
- Display temperature, date, time, and smoke status on a 16×2 LCD.
- Generate buzzer alerts for high-temperature and smoke conditions.
- Upload sensor values and alert events to ThingSpeak using ESP-01 Wi-Fi.
- Store the temperature threshold in EEPROM.
- Allow the user to configure time, date, weekday, and temperature threshold through a keypad.
- Use interrupts for switch input and UART reception.

## ⚙️ Hardware Requirements

| Component | Purpose |
|---|---|
| LPC21xx ARM7 Microcontroller | Main controller |
| LM35 Temperature Sensor | Measures temperature |
| MQ-2 Gas/Smoke Sensor | Detects smoke/gas |
| ESP-01 / ESP8266 | Wi-Fi communication |
| 16×2 LCD | Displays system information |
| 4×4 Matrix Keypad | User input and configuration |
| RTC | Maintains date and time |
| AT24C256 / I²C EEPROM | Stores configuration data |
| Buzzer | Provides alert indication |
| UART Interface | Serial communication with ESP-01 |
| Power Supply | Provides required operating power |

> **Note:** The source files contain references to both `24lc01.h` and AT24C256-related I²C code. Use the EEPROM device that matches the actual hardware configuration of your project.

## 💻 Software Requirements

- **Embedded C**
- **Keil C Compiler**
- **Flash Magic** for programming the ARM7 microcontroller
- UART terminal/serial interface for debugging and communication
- **ThingSpeak** cloud platform for remote monitoring

## 🧩 Technologies / Protocols Used

- ARM7 Embedded System
- ADC
- GPIO
- UART
- I²C
- RTC
- External Interrupts
- ESP8266 AT Commands
- TCP/IP
- HTTP
- ThingSpeak API

## 🔄 Working / Project Flow

```text
          ┌──────────────────────┐
          │   LPC21xx ARM7 MCU   │
          └──────────┬───────────┘
                     │
        ┌────────────┼─────────────┐
        │            │             │
        ▼            ▼             ▼
     LM35          MQ-2           RTC
 Temperature     Smoke/Gas     Date & Time
        │            │             │
        └────────────┼─────────────┘
                     │
                     ▼
              ┌─────────────┐
              │     LCD     │
              └─────────────┘
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
       Keypad                 Buzzer
          │
          ▼
       EEPROM
          │
          └──────────────┐
                         ▼
                   ┌──────────┐
                   │  ESP-01  │
                   │ ESP8266  │
                   └────┬─────┘
                        │ Wi-Fi
                        ▼
                 ┌─────────────┐
                 │ ThingSpeak  │
                 │   Cloud     │
                 └─────────────┘
```

### Step-by-Step Operation

1. The LPC21xx ARM7 microcontroller initializes the LCD, UART, ADC, RTC, I²C, keypad, interrupts, MQ-2 sensor, and ESP-01 module.
2. The RTC is configured with the initial time and date.
3. The temperature threshold is read from EEPROM.
4. The LM35 produces an analog voltage proportional to temperature.
5. The ADC converts the analog LM35 signal into a digital value, which is processed by the microcontroller.
6. The current temperature and RTC information are displayed on the LCD.
7. The MQ-2 sensor is monitored through a GPIO input to determine smoke/gas status.
8. When the temperature exceeds the configured threshold, the temperature buzzer is activated and an alert is uploaded.
9. When a smoke condition is detected, the smoke buzzer is activated and a smoke alert is sent to ThingSpeak.
10. When the smoke condition changes, the corresponding event is uploaded to the cloud.
11. Temperature data is periodically uploaded to ThingSpeak.
12. The keypad allows the user to edit the time, date, weekday, and temperature threshold.
13. UART interrupts receive data from the ESP-01 and store received characters in a buffer.
14. The ESP-01 communicates with the ThingSpeak server using TCP and HTTP requests.

## 🌡️ Temperature Monitoring

The **LM35** is connected to an ADC input of the LPC21xx controller. The controller reads the analog voltage and converts it into a temperature value.

The system compares the measured temperature with the configured threshold:

```text
Measured Temperature > Threshold
             │
             ▼
       High Temperature
             │
        ┌────┴────┐
        ▼         ▼
     Buzzer    Cloud Alert
```

The threshold can be configured through the keypad and stored in EEPROM.

## 💨 Smoke Detection

The MQ-2 sensor is connected as a digital smoke/gas detection input. The microcontroller continuously checks the sensor status.

```text
Smoke Detected
      │
      ├──► LCD Alert
      ├──► Buzzer ON
      └──► ThingSpeak Alert
```

When the smoke condition is cleared, the system detects the status change and can upload the corresponding smoke-release event.

## 🌐 ESP-01 and ThingSpeak Communication

The ESP-01 is controlled by the LPC21xx through **UART** using ESP8266 AT commands.

The communication sequence includes commands such as:

```text
AT
ATE0
AT+CIPMUX=0
AT+CWJAP="SSID","PASSWORD"
AT+CIPSTART="TCP","api.thingspeak.com",80
AT+CIPSEND=<length>
GET /update?api_key=YOUR_API_KEY&field...
```

The project source code contains a ThingSpeak API key and Wi-Fi credentials. **For GitHub, do not publish real Wi-Fi passwords or API keys. Replace them with placeholders before making the repository public.**

Example:

```text
YOUR_WIFI_SSID
YOUR_WIFI_PASSWORD
YOUR_THINGSPEAK_API_KEY
```

## 📊 ThingSpeak Fields

The source code uses different ThingSpeak fields for different types of information:

| Field | Information |
|---|---|
| Field 1 | Periodic temperature data |
| Field 3 | Smoke status / smoke event |
| Field 4 | High-temperature alert |

The exact ThingSpeak channel configuration should match the field numbers used in the source code.

## ⏱️ Periodic Data Upload

The main program checks the RTC minute and second values and periodically sends the temperature value to ThingSpeak. The source code is configured to upload temperature data at a **3-minute interval**.

## ⌨️ Keypad Menu

The keypad provides menu-based configuration options, including:

- Edit hour
- Edit minute
- Edit seconds
- Edit day
- Edit month
- Edit year
- Edit weekday
- Configure temperature threshold

Range checks are included for time and date values. Invalid values result in an **"out of range"** message on the LCD.

## 💾 EEPROM Storage

The system uses I²C EEPROM to store configuration information such as the temperature threshold. This allows the configured threshold to be retained and read during system startup.

## ⏰ RTC Function

The RTC maintains:

- Hours
- Minutes
- Seconds
- Day
- Month
- Year
- Weekday

The current date and time are displayed on the LCD.

## 🔔 Alert System

The project provides two main alert mechanisms:

### High Temperature Alert
- Compares measured temperature with the configured threshold.
- Activates the temperature buzzer when the threshold is exceeded.
- Sends a high-temperature event to ThingSpeak.

### Smoke Alert
- Monitors the MQ-2 sensor.
- Activates the smoke buzzer when smoke is detected.
- Displays the smoke status on the LCD.
- Sends the smoke event to ThingSpeak.

## 📂 Project Structure

```text
kick/
│
├── adc (1).c
├── adc (1).h
├── adc_rtc_main.c
├── at24c256.c
├── at24c256.h
├── clock.h
├── cust_lcd.c
├── cust_lcd.h
├── delay (1).h
├── delay.c
├── esp01 (1).h
├── esp01.c
├── esp01_call.c
├── i2c.c
├── i2c.h
├── interrupt.c
├── interrupt.h
├── keypad (1).c
├── keypad_defines.h
├── lcd_defines (1).h
├── lcd_driver.c
├── menu.h
├── menu_new.c
├── mq2.h
├── rtc (1).h
├── rtc (2).c
├── smoke.c
├── uart.c
├── uart.h
└── uart_main.c
```

### Important Source Files

| File | Function |
|---|---|
| `adc (1).c/.h` | ADC configuration and sensor reading |
| `rtc (2).c` / `rtc (1).h` | RTC initialization, date/time handling and LCD display |
| `lcd_driver.c` | LCD driver functions |
| `keypad (1).c` | Matrix keypad interface |
| `menu_new.c` | Configuration menu and keypad input |
| `esp01.c` | ESP-01 Wi-Fi and ThingSpeak communication |
| `esp01_call.c` | Cloud update/event handling |
| `uart.c/.h` | UART0 transmit and receive functions |
| `uart_main.c` | UART receive interrupt service routine |
| `i2c.c/.h` | I²C communication |
| `at24c256.c/.h` | EEPROM-related functions |
| `smoke.c` / `mq2.h` | MQ-2 smoke sensor interface |
| `interrupt.c/.h` | External interrupt handling |
| `delay.c` | Delay functions |

## ▶️ How to Build and Run

1. Extract the project ZIP file.
2. Open the source files in **Keil µVision**.
3. Create/configure the appropriate LPC21xx target device.
4. Add all required `.c` and `.h` files to the Keil project.
5. Configure the required microcontroller clock and peripheral settings.
6. Configure the ESP-01 Wi-Fi credentials and ThingSpeak API key in the ESP-01 source code.
7. Compile the project and resolve any device/header or library configuration differences required by your specific Keil environment.
8. Generate the HEX file.
9. Connect the LPC21xx board to the programming interface.
10. Use **Flash Magic** to program the generated HEX file into the microcontroller.
11. Connect the sensors, LCD, keypad, EEPROM, buzzer, RTC and ESP-01 according to the hardware circuit.
12. Power on the system.
13. Set the required RTC values and temperature threshold through the keypad.
14. Observe temperature, date/time and smoke status on the LCD.
15. Verify uploaded values and alerts in the configured ThingSpeak channel.

## 📊 Expected Output

The LCD provides information similar to:

```text
HH:MM:SS
DD/MM/YY

Temperature: XX.X
SMOKE: 0/1
```

During abnormal conditions, the display can show messages such as:

```text
SMOKE ALERT
```

or:

```text
SMOKE RELEASE
```

During ESP-01 initialization, the LCD displays a startup message indicating ESP-01 initialization.

## 🚀 Applications

- Industrial temperature monitoring
- Smoke and gas detection systems
- Remote environmental monitoring
- IoT-based safety systems
- Equipment monitoring
- Smart laboratories
- Embedded system learning and academic projects
- Remote alert and sensor monitoring applications

## ⭐ Key Features

- Real-time temperature monitoring
- Smoke/gas detection
- RTC-based date and time
- 16×2 LCD display
- Keypad-based configuration
- EEPROM threshold storage
- ESP-01 Wi-Fi connectivity
- ThingSpeak cloud integration
- UART communication
- I²C communication
- ADC-based temperature measurement
- External interrupt handling
- Buzzer-based alerts
- Periodic cloud data upload

## ⚠️ Security Note

Before uploading this project to a **public GitHub repository**, remove or replace:

- Wi-Fi SSID
- Wi-Fi password
- ThingSpeak API keys
- Any other private credentials

Use placeholders such as:

```text
YOUR_WIFI_SSID
YOUR_WIFI_PASSWORD
YOUR_THINGSPEAK_API_KEY
```

## 👤 Author

**Sony Myadari**

Embedded Systems / ARM7 / IoT Project

---

## 📜 Project Summary

This project demonstrates an embedded IoT monitoring system that combines **ARM7 microcontroller programming, ADC, GPIO, UART, I²C, RTC, interrupts, EEPROM, sensor interfacing, ESP8266 Wi-Fi communication, and ThingSpeak cloud connectivity** to provide real-time temperature and smoke monitoring with local and remote alerts.
