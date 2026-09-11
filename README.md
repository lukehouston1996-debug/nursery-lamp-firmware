# 🎈 Nursery Hot Air Balloon Lamp

An open-source **ESP32-powered hot air balloon nursery lamp** featuring addressable RGB lighting, music playback, physical rotary controls, Wi-Fi connectivity, and automatic over-the-air (OTA) firmware updates.

This project started as a DIY nursery light and evolved into a connected electronics project combining lighting, audio, physical controls and remote firmware updates.

The goal is simple: create something that feels like a decorative nursery lamp while providing gentle ambient lighting and calming music.

---

## ✨ Features

* 🎈 Hot air balloon-inspired nursery lamp
* 🌈 WS2812B individually addressable RGB LEDs
* 💡 Multiple built-in lighting modes
* 🔆 Dedicated rotary encoder for brightness
* 🔘 Single-click and double-click light controls
* 🎵 DFPlayer Mini audio playback
* 🎚️ Dedicated rotary encoder for music controls
* 🔊 External speaker
* 😴 Sleep timer
* 📶 Wi-Fi connectivity
* 🔄 Automatic OTA firmware updates from GitHub
* 🧩 WiFiManager for easy Wi-Fi setup
* 💾 Lullabies stored locally on a microSD card
* 🔌 USB powered

---

# 🛠️ Hardware

## Main components

| Component         | Purpose                           |
| ----------------- | --------------------------------- |
| ESP32             | Main microcontroller              |
| WS2812B LED strip | Addressable lighting              |
| DFPlayer Mini     | MP3 playback                      |
| 3W 4Ω speaker     | Audio output                      |
| Rotary encoder #1 | Light brightness and mode control |
| Rotary encoder #2 | Music control                     |
| MicroSD card      | Stores MP3 files                  |
| 1000µF capacitor  | LED power smoothing               |
| 330Ω resistor     | LED data protection               |

The physical construction is flexible. The electronics can be adapted to different lamp bodies, enclosures or decorative designs.

---

# 🎛️ Controls

The lamp uses **two rotary encoders** — one dedicated to lighting and one dedicated to music.

## 💡 Light rotary encoder

### Rotate

Adjusts the brightness of the LEDs.

### Single press

Moves to the next lighting mode.

### Double press

Toggles the lights **OFF / ON**.

When the lights are turned back on, the previously selected lighting mode is restored.

This makes it possible to turn the lights off instantly without cycling through all of the lighting modes.

---

## 🎵 Music rotary encoder

The second rotary encoder is dedicated to the music system.

Its functionality can be customised in the Arduino sketch.

The current project uses the encoder to provide physical control over music playback.

---

# 🌈 Lighting

The lamp uses a WS2812B individually addressable LED strip.

The current firmware contains multiple predefined lighting modes.

These can include:

* Warm ambient lighting
* Soft colour transitions
* Calm nursery colours
* Slowly changing colours
* Static colours
* Sleep-friendly modes
* Lights-off mode

Lighting behaviour is controlled by the ESP32.

New lighting modes can be added by extending the lighting logic in the Arduino sketch.

---

# 🎵 Music

Audio playback is handled by a **DFPlayer Mini**.

Music files are stored locally on a microSD card.

For example:

```text
SD CARD
└── 0001.mp3
```

Additional tracks can be added using the DFPlayer's supported file naming structure.

Because the music is stored locally, the lamp does not need an internet connection to play audio once the firmware and music files are installed.

---

# 📶 Wi-Fi

The ESP32 uses **WiFiManager** to handle Wi-Fi configuration.

Wi-Fi credentials do not need to be hard-coded into the firmware.

If the lamp cannot connect to a previously configured network, WiFiManager can provide a configuration portal allowing a new network to be selected.

This makes the project easier to configure and move between different networks.

---

# 🔄 OTA Firmware Updates

One of the main features of this project is **Over-The-Air (OTA) firmware updating**.

Once the lamp has been configured and connected to Wi-Fi, firmware can be updated remotely without physically connecting the ESP32 to a computer.

The ESP32 checks a GitHub repository for the latest firmware version.

The firmware repository contains:

```text
nursery-lamp-firmware/
│
├── firmware.bin
└── version.txt
```

`version.txt` contains the currently published firmware version.

For example:

```text
1.0.2
```

The ESP32 compares this version against its locally installed firmware version.

If a newer version is available, the ESP32 downloads the new firmware binary, installs it and reboots.

### OTA process

```text
ESP32 starts
     │
     ▼
Connect to Wi-Fi
     │
     ▼
Check version.txt
     │
     ▼
Is GitHub version newer?
     │
   ┌─┴─┐
   │   │
  NO  YES
   │   │
   │   ▼
   │ Download firmware.bin
   │   │
   │   ▼
   │ Install firmware
   │   │
   │   ▼
   └── Reboot
```

This means that future firmware updates can be delivered without physically accessing the ESP32.

---

# 🧑‍💻 Software

The project is built using:

* Arduino IDE
* ESP32 Arduino core
* FastLED
* DFPlayer Mini library
* WiFiManager
* ESP32 OTA functionality
* GitHub for firmware distribution

---

# 📁 Repository Structure

The OTA firmware repository is intentionally simple:

```text
nursery-lamp-firmware/
│
├── firmware.bin
└── version.txt
```

The source code can be maintained separately from the published firmware binary.

---

# 🚀 Building the Firmware

## 1. Install Arduino IDE

Install the Arduino IDE and configure it for your ESP32 board.

## 2. Install the required libraries

Install the libraries used by the project through the Arduino Library Manager.

## 3. Open the sketch

Open the latest Arduino sketch.

The main firmware version is defined near the top of the sketch:

```cpp
const char* FIRMWARE_VERSION = "1.0.2";
```

## 4. Configure the hardware

The current build uses the following GPIO assignments:

```text
WS2812B LED strip
LED data = GPIO 5

DFPlayer Mini
ESP32 RX = GPIO 27
ESP32 TX = GPIO 26

Music rotary encoder
CLK = GPIO 18
DT  = GPIO 19
SW  = GPIO 21

Light rotary encoder
CLK = GPIO 22
DT  = GPIO 23
SW  = GPIO 25
```

These pins can be changed to suit different hardware configurations.

---

# 📦 Creating a Firmware Release

When creating a new firmware version:

### 1. Update the firmware version

For example:

```cpp
const char* FIRMWARE_VERSION = "1.0.3";
```

### 2. Verify the sketch

Compile the project in Arduino IDE and make sure there are no errors.

### 3. Export the binary

Use:

**Sketch → Export Compiled Binary**

Arduino will generate a `.bin` file based on the sketch name.

For example:

```text
Hot_air_balloon01.ino.bin
```

### 4. Create the OTA binary

Make a copy of the exported binary and rename the copy:

```text
firmware.bin
```

The OTA system expects this exact filename.

### 5. Upload the new binary

Replace the existing `firmware.bin` in the firmware repository.

### 6. Update the version file

Only after the new binary has been uploaded, update:

```text
version.txt
```

For example:

```text
1.0.3
```

### ⚠️ Important

The firmware binary should be uploaded **before** changing `version.txt`.

Otherwise, the ESP32 could detect a new version number while downloading an old firmware binary.

---

# 🧪 OTA Testing

OTA updates can be tested by deliberately increasing the firmware version.

For example:

```text
ESP32 installed: 1.0.1
GitHub version:  1.0.2
```

The ESP32 should detect the update and download the new firmware.

The Serial Monitor should report something similar to:

```text
Installed version: 1.0.1
GitHub version: 1.0.2

NEW FIRMWARE AVAILABLE

Updating to: 1.0.2
Downloading firmware.bin...
```

After the update, the ESP32 should reboot.

It should then report:

```text
Installed version: 1.0.2
GitHub version: 1.0.2
Firmware is up to date.
```

---

# 🔌 Power

The ESP32 can be powered through USB.

During development, the ESP32 can be connected directly to a computer.

Once the firmware is installed and Wi-Fi has been configured, the lamp can be powered independently from the computer.

OTA updates do not require the ESP32 to remain connected to a computer.

The only requirement for an OTA update is that the lamp has:

* Power
* Wi-Fi connectivity
* Access to the firmware repository

---

# 🔐 Security

The project is designed so that Wi-Fi credentials do not need to be stored directly in the source code.

If you fork or modify the project, **do not commit:**

* Wi-Fi passwords
* API keys
* GitHub tokens
* Private credentials
* Personal information

The OTA firmware repository is public because the ESP32 needs to be able to retrieve the published firmware.

For a more advanced production implementation, firmware signing and verification could be added to protect against unauthorised firmware replacement.

---

# 🎨 Physical Design

The electronics are designed to fit inside a decorative hot-air-balloon-style lamp.

A simplified layout might look like:

```text
             🎈
        ┌───────────┐
       /             \
      /   LED STRIP   \
     /                 \
    /___________________\
          │       │
          │       │
       ESP32   Speaker
          │
      DFPlayer
          │
    ┌─────┴─────┐
    │           │
 Light knob   Music knob
```

The exact physical construction is intentionally flexible so makers can adapt the electronics to their own lamp design.

---

# 💡 Future Ideas

There are plenty of possible directions for future versions.

Some ideas include:

* 📱 Mobile/web control
* 🌐 Local web interface
* 🎨 User-created lighting modes
* 🎵 Playlist support
* ⏰ Configurable sleep schedules
* 🌙 Automatic night mode
* 🌡️ Temperature monitoring
* 🔋 Battery operation
* 📊 OTA update history
* 🔐 Signed firmware updates
* 🖥️ Web dashboard
* 🧠 Adaptive lighting
* ✨ Additional lighting animations
* 🎚️ More advanced music controls

---

# 🤝 Contributing

Pull requests, improvements and ideas are welcome.

If you build your own version, feel free to adapt the hardware, enclosure and firmware to suit your needs.

Useful contributions could include:

* New lighting effects
* Improved rotary controls
* Hardware adaptations
* Better power management
* Documentation
* Bug fixes
* Additional audio functionality
* OTA improvements
* Alternative enclosure designs

If you make your own version, sharing photos or build notes can also help other makers reproduce the project.

---

# ⚠️ Disclaimer

This is a DIY electronics project.

Use appropriate electrical safety precautions when assembling and powering the lamp.

Do not leave DIY electronics unattended where overheating, short circuits or component failure could create a hazard.

Particular care should be taken when using the project in a nursery or around children.

---

# 📜 Licence

This project is open source.

Choose an appropriate licence before publishing derivative versions.

A permissive licence such as the **MIT License** may be suitable if you want others to freely use, modify and redistribute the software.

---

# 🎈 Why this project?

This started with a simple idea:

> **Build a calming nursery light that feels like something you'd actually want to keep in the room.**

It grew into an experiment combining physical design, electronics, lighting, audio and connected firmware into one small project.

The project is intentionally approachable so that other beginners can experiment with ESP32, addressable LEDs, audio modules and OTA firmware without needing to build an overly complicated system.

**Build it. Change it. Make it yours.**
