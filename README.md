# ECE362 — Junior Design Line-Following Car

An ESP32-based vehicle project for **ECE362 Junior Design (2023)**. The Arduino firmware combines HUSKYLENS visual guidance, PID steering, motor control, INA219 power measurements, and Bluetooth serial telemetry. A Python recorder captures the vehicle's serial output.

Original project name: **JrDesn-Vehicle-Project**. The original ECE362 setup and upload workflow is retained below and supplemented with a code and hardware guide.

## System overview

```text
HUSKYLENS arrow position → ESP32 control loop → steering servo and motor
INA219 measurements     → Bluetooth serial → Python recorder → local CSV log
```

The firmware reads the arrow's target X coordinate, compares it with the image center, and uses a PID controller to update steering. It selects a lower speed for larger steering offsets and includes logic to stop when the expected arrow is lost. These descriptions reflect the code; they are not new performance or safety validation results.

## Repository guide

| Path | Purpose |
| --- | --- |
| [main/main.ino](main/main.ino) | Arduino/ESP32 firmware |
| [python/main.py](python/main.py) | Python serial telemetry recorder |
| [.vscode/tasks.json](.vscode/tasks.json) | Upload and logging tasks |
| [python/](python/) | Recorder plus the original checked-in Windows virtual-environment files |
| [LICENSE](LICENSE) | Existing MIT license |

## Original setup and usage

### Setting up

1. Install the Arduino CLI and an Arduino extension for VS Code.
2. Install Python 3.9 or later.
3. Install PuTTY if you want to test the serial connection.
4. Activate the virtual environment in the terminal and select the corresponding Python interpreter in the editor to resolve missing-package diagnostics.

### Uploading and logging

To upload code and start logging, press `Ctrl + Shift + P`, select **Tasks: Run Task**, and choose **Upload & Start Logging**.

After uploading, the original workflow provides a **ten-second window** to unplug the car from the computer for logging to work. It then provides a **thirty-second window** for the terminal to connect and start recording.

The tasks invoke the existing `arduino.upload` editor command, then start the recorder. This assumes the corresponding extension command is available and the correct board and upload port have been configured.

## Hardware and libraries

The source references the following components:

| Component | Role / code configuration |
| --- | --- |
| ESP32 | Main controller and Bluetooth serial link |
| HUSKYLENS | Arrow tracking over I²C; source defines address `0x32`, SDA `21`, and SCL `22` |
| Steering servo | Pin `32` in the sketch |
| Motor control | Pin `33` in the sketch |
| INA219 | Voltage, current, and power readings |

The sketch includes `analogWrite.h`, `ESP32PWM.h`, `ESP32Servo.h`, `ESP32Tone.h`, `FastPID.h`, `FRAM_RINGBUFFER.h`, `FRAM.h`, `Wire.h`, `Adafruit_INA219.h`, `BluetoothSerial.h`, and `HUSKYLENS.h`. Install the corresponding libraries and ESP32 board support for the actual hardware. Exact historical dependency versions were not recorded.

FRAM headers and a FRAM logging TODO are present, so their inclusion should not be interpreted as evidence that FRAM logging is complete.

## Recorder details and a fresh Python environment

`python/main.py` imports **pyserial** and otherwise uses Python's standard library. The current script:

- Accepts a numeric Windows port argument, such as `7`, and constructs `COM7`.
- Connects at **115200 baud** and retries during a thirty-second window.
- Writes a timestamped file ending in `Device Logs.csv` in the current working directory.
- Records received ASCII telemetry and can be interrupted with Ctrl+C.

The existing VS Code task uses port `7` and paths under `python/Scripts/`. Update those local settings if the actual port or environment differs. The checked-in environment is specific to the original Windows machine; it is not a portable dependency installation.

For a fresh Windows environment, run these commands from the repository root:

```powershell
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install pyserial
.\.venv\Scripts\python.exe python/main.py 7
```

Replace `7` with the actual Windows serial/Bluetooth COM port number. This manual command runs the recorder only; it does not upload firmware or add the task's ten-second delay. Using the new `.venv` with the original combined task requires adjusting that task's interpreter/activation path. The recorder currently assumes Windows COM ports, so Linux/macOS device paths require a code change.

## Telemetry and development context

The firmware's telemetry output includes a timestamp and INA219 measurements such as bus voltage, shunt voltage, load voltage, current, and power. Review the sketch's format string and units when processing a recording.

The source retains original authorship comments naming Jake Armstrong and Garrett Hart, and the repository retains its upstream project history and license. Board wiring, library versions, and logging behavior should be checked against the actual vehicle before reproducing the experiment.

## Original README reference

For continuity, the original setup text is also preserved verbatim below.

<details>
<summary>Original project README</summary>

# JrDesn-Vehicle-Project

 ECE362 Code for 2023

## Setting Up

1. Install the Arduino CLI & Arduino Extension for VSCode.
2. Install Python 3.9+
3. Install PuTTY if you want to test
4. Activate the Venv terminal if you want to get rid of the erorr squiggles.

## Usage

To upload code & start logging, run `Ctrl + Shift + P` and press `Tasks: Run task`, then select `Upload & Start Logging`.
After uploading there is a ten second window for you to unplug the car from your computer for logging to work. Then, there's a 30 second window for the terminal to start logging.

</details>
