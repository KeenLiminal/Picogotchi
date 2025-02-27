# Picogotchi
An outline on creating a Raspberry Pi Pico Virtual Per
# Building a Tamagotchi-Like Virtual Pet with Raspberry Pi Pico

Creating a virtual pet using a **Raspberry Pi Pico** is a fun and educational project that combines hardware assembly, programming, and game design. This article provides a comprehensive guide covering component selection, circuit assembly, software development, enclosure design, and testing.

---

## 1. Materials and Estimated Costs

| Component                         | Purpose                                             | Estimated Cost (USD) |
|-----------------------------------|-----------------------------------------------------|----------------------|
| **Raspberry Pi Pico**             | Microcontroller to run the virtual pet logic        | $4.00                |
| **SSD1306 128x64 OLED Display**   | Display for pet animations and status               | $6.99                |
| **Tactile Push Buttons (x3)**     | User interaction (e.g., feeding, playing)           | $0.30                |
| **Breadboard & Jumper Wires**     | Temporary prototyping and wiring                    | $8.00                |
| **3D-Printed or Custom Enclosure**| Protective case for the project                     | $10.00               |
| **Miscellaneous Supplies**        | Soldering materials, resistors, etc.                | $5.00                |
| **Total Estimated Cost**          | —                                                   | **$29.29**           |

> **Note:** Prices are approximate and may vary by retailer and location. Purchasing components in bulk or kits may reduce overall costs.

---

## 2. Assembling the Hardware

### Step 1: Connect the OLED Display

The **SSD1306 OLED Display** is an I2C-based module that shows your virtual pet.

**Wiring Instructions:**
- **VCC**: Connect to the **3.3V** pin on the Raspberry Pi Pico.
- **GND**: Connect to a **GND** pin on the Pico.
- **SDA**: Connect to **GPIO0 (I2C0 SDA)**.
- **SCL**: Connect to **GPIO1 (I2C0 SCL)**.

### Step 2: Connect the Buttons

The **tactile push buttons** allow you to interact with the pet.

**Wiring Instructions:**
- **Button A (Feed the pet)**: Connect one leg to **GPIO2** and the other to **GND**.
- **Button B (Play with the pet)**: Connect one leg to **GPIO3** and the other to **GND**.
- **Button X (Check pet status)**: Connect one leg to **GPIO4** and the other to **GND**.

> **Debounce Consideration:**  
> Use a capacitor or implement software debounce to avoid false triggers.

### Step 3: Powering the Circuit

- **USB Power**: Connect the Raspberry Pi Pico to a computer using a micro-USB cable.
- **Battery Option**: For portability, use a 3.7V LiPo battery with a 3.3V regulator.

---

## 3. Setting Up the Software

### Step 1: Install MicroPython on the Pico

MicroPython is a lightweight Python implementation for microcontrollers.

**Installation Steps:**
1. Download the latest MicroPython firmware for Raspberry Pi Pico from the [MicroPython website](https://micropython.org/download/RPI_PICO/).
2. Hold the **BOOTSEL** button on the Pico and connect it to your computer.
3. Drag and drop the downloaded **UF2 file** onto the Pico’s USB storage.
4. Open the **Thonny IDE**, select the **MicroPython (Raspberry Pi Pico)** interpreter, and verify the connection.

---

## 4. Programming the Virtual Pet

### Step 1: Install Required Libraries

Before writing your code, ensure you have the necessary libraries.

- **SSD1306 OLED Library:**  
  Download the `ssd1306.py` file from [GitHub](https://github.com/micropython/micropython/blob/master/drivers/display/ssd1306.py) and save it to your Pico using Thonny.

### Step 2: Write the Virtual Pet Code

The virtual pet program will include:
- **Display Initialization**
- **Button Handling**
- **Graphics and Animations**
- **Game Logic (feeding, playing, status management)**

Below is a simplified example code:

```python
from machine import Pin, I2C
import ssd1306
import utime

# Initialize I2C and OLED display
i2c = I2C(0, scl=Pin(1), sda=Pin(0), freq=400000)
oled = ssd1306.SSD1306_I2C(128, 64, i2c)

# Define buttons
button_feed = Pin(2, Pin.IN, Pin.PULL_DOWN)
button_play = Pin(3, Pin.IN, Pin.PULL_DOWN)
button_status = Pin(4, Pin.IN, Pin.PULL_DOWN)

# Pet attributes
hunger = 5
happiness = 5

def draw_pet():
    oled.fill(0)
    oled.text("Pet:", 40, 10)
    oled.text("Hunger: " + str(hunger), 20, 30)
    oled.text("Happiness: " + str(happiness), 20, 40)
    oled.show()

# Button Functions
def feed_pet(pin):
    global hunger
    if hunger > 0:
        hunger -= 1
    draw_pet()

def play_pet(pin):
    global happiness
    if happiness < 10:
        happiness += 1
    draw_pet()

# Assign button interrupts
button_feed.irq(trigger=Pin.IRQ_FALLING, handler=feed_pet)
button_play.irq(trigger=Pin.IRQ_FALLING, handler=play_pet)

# Main loop
while True:
    draw_pet()
    utime.sleep(1)
