# Woody Feed Indicator

A Wi-Fi connected ESP32 device that tracks whether a dog has been fed, with automatic feed windows, visual LED indicators, an OLED display, and push notifications via ntfy.sh.

Built as a practical solution to the household problem of not knowing if the dog had been fed.

---

## Features

- **Four LED indicators** showing feed status at a glance
- **OLED display** showing time, date, Wi-Fi status, and feed information
- **Automatic feed windows** -- LEDs and display change state based on configured feeding times
- **Manual button** to mark the dog as fed
- **Auto-reset** at configured times to clear feed status for the next meal
- **Missed feed indicator** -- orange and red LEDs if a feed window closes without a feed
- **Tablet reminder** -- blue LED and display prompt on a configured day of the month
- **Push notifications** via ntfy.sh for fed, tablet due, tablet given, and optionally missed feed
- **Wi-Fi and NTP** for accurate timekeeping with daily resync and background reconnection
- **Simulation mode** for testing without real hardware using Wokwi
- **Debug logging** via serial monitor

---

## LED States

| Situation | LEDs |
|---|---|
| Waiting for time sync | Orange |
| Outside feed time, not fed | Orange |
| Outside feed time, feed missed | Orange + Red |
| Outside feed time, fed | Orange + Green |
| In feed time, not fed | Red |
| In feed time, not fed (tablet day) | Red + Blue |
| In feed time, fed | Green |
| In feed time, fed, tablet pending | Green + Blue |

---

## Push Notifications

Notifications are delivered via [ntfy.sh](https://ntfy.sh) to the ntfy app on iOS or Android.

| Event | Message |
|---|---|
| Fed | "[Name] has been fed at HH:MM" |
| Tablet due | "[Name] tablet is due today" (sent when morning feed time opens) |
| Tablet given | "[Name] tablet given at HH:MM" |
| Feed missed | "[Name] has not been fed" (configurable on/off) |

Notifications are disabled in simulation mode and logged to serial instead.

---

## Hardware

| Component | Quantity |
|---|---|
| ESP32 DevKit V1 (WROOM-32) | 1 |
| Red LED | 1 |
| Green LED | 1 |
| Orange LED | 1 |
| Blue LED | 1 |
| Resistors ~1.6kΩ (100Ω for brighter LEDs) | 4 |
| Push button | 1 |
| SSD1306 0.96" OLED display (I2C) | 1 |
| Breadboard and jumper wires | -- |

---

## Wiring

| Pin | Component |
|---|---|
| D4 | Push button (other leg to GND) |
| D18 | Green LED (via resistor, cathode to GND) |
| D19 | Red LED (via resistor, cathode to GND) |
| D21 | OLED SDA |
| D22 | OLED SCL |
| D25 | Orange LED (via resistor, cathode to GND) |
| D26 | Blue LED (via resistor, cathode to GND) |
| 3.3V | OLED VCC |
| GND | All cathodes, button, OLED GND |

---

## Configuration

All user-configurable settings are at the top of the sketch:

```cpp
// Dog's name
#define DOG_NAME "Woody"

// WiFi credentials
#define WIFI_SSID "your_wifi_ssid"
#define WIFI_PASS "your_wifi_pass"

// Timezone (POSIX string -- change for non-UK use)
#define TIMEZONE "GMT0BST,M3.5.0/1,M10.5.0"

// Push notifications
#define NTFY_TOPIC       "your-ntfy-topic"  // Your ntfy.sh topic name
#define NTFY_FEED_MISSED true               // true = notify if feed missed

// Feeding windows
const FeedWindow WINDOWS[] = {
  {  6,  0,  8,  0, true  },   // Morning
  { 12,  0, 13,  0, false },   // Optional lunch (disabled)
  { 17, 30, 20,  0, true  }    // Evening
};

// Reset times
const ResetTime RESETS[] = {
  { 11, 30, true,  false },    // After morning feed
  { 15,  0, false, false },    // Optional lunch reset (disabled)
  { 23, 30, true,  true  }     // End of day
};

// Tablet settings
#define TABLET_FEATURE true
#define TABLET_DAY     1        // Day of month tablet is due (1 = 1st)
#define TABLET_WINDOW  0        // Feed window that includes the tablet (0 = morning)
```

---

## Debug Flags

```cpp
#define DEBUG_SIM false   // true = simulated clock, Wokwi WiFi, notifications logged only
#define DEBUG_LOG true    // true = verbose serial output at 115200 baud
```

Set `DEBUG_SIM true` to test in [Wokwi](https://wokwi.com/esp32) without real hardware.

---

## Push Notification Setup

1. Install the **ntfy** app on iOS or Android
2. Choose a topic name -- use a random string for privacy e.g. `woody-feed-xk7p2m9q`
3. Subscribe to your topic in the app
4. Set `NTFY_TOPIC` in the code to match
5. No account required for basic use

---

## Dependencies

Install via Arduino IDE Library Manager:

- [Adafruit SSD1306](https://github.com/adafruit/Adafruit_SSD1306)
- [Adafruit GFX Library](https://github.com/adafruit/Adafruit-GFX-Library)

ESP32 board support via Boards Manager -- add this URL under File → Preferences:
```
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
```

---

## Uploading

1. Open the sketch in Arduino IDE
2. Set `WIFI_SSID`, `WIFI_PASS`, and `NTFY_TOPIC` in the settings section
3. Select **Tools → Board → ESP32 Arduino → ESP32 Dev Module**
4. Select **Tools → Port → your COM port**
5. Click Upload

If the upload hangs at "Connecting.....", hold the **BOOT** button on the ESP32 until uploading begins.

---

## Tested With

- ESP32 DevKit V1 (CP2102 USB chip)
- Wokwi simulator (simulation mode)
- Arduino IDE 2.x
- ntfy.sh push notifications (iOS and Android)

---

## Future Plans

- PIR sensor to detect food box lid opening automatically
- Permanent build on perfboard in project enclosure
- Battery power with 18650 cell
- Touch screen display
- Third feed window support (code already included, disabled by default)
- GitHub Actions for automated builds

---

## Licence

MIT -- free to use, adapt, and share.
