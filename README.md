# Automatic Air Bag Release System

## Overview
The **Automatic Air Bag Release System** is designed to dynamically adjust and deploy an airbag based on pressure or impact detection. This system utilizes an **AVR microcontroller** to control a **PWM-driven airbag release mechanism**, ensuring safe and effective deployment.

The system incorporates:
- **PWM (Pulse Width Modulation)** to control airbag inflation
- **Touchpad Sensor** to detect activation
- **Timers** for delay-based duty cycle adjustments
- **Interrupt-based control** for precise timing

## Features
- **Smooth PWM control** for gradual airbag inflation and deflation.
- **Touchpad-based activation** for hands-free operation.
- **Dynamic duty cycle adjustment** for controlled inflation speed.
- **Interrupt-driven timing** to ensure precise operation.
- **Fail-safe mechanism** to prevent false triggers.

## Hardware Requirements
- **ATmega328P** (or similar AVR microcontroller)
- **16MHz Crystal Oscillator**
- **Touchpad Sensor** (capacitive or mechanical)
- **Airbag Actuator (PWM-controlled valve or motor)**
- **LEDs** (for debugging and status indication)
- **Resistors, Capacitors, Jumper Wires**
- **Power Supply (5V)**

## Software Requirements
- **AVR-GCC Compiler**
- **AVRDUDE** (for flashing the firmware)
- **Atmel Studio / VS Code with PlatformIO** (Optional)
- **Makefile / CMake (for compilation and flashing)**

---
## System Working Principle
The system operates as follows:
1. The **touchpad sensor** detects an activation signal.
2. If triggered, **Timer0** generates a PWM signal to control the airbag actuator.
3. The duty cycle of the PWM signal **gradually increases** to inflate the airbag smoothly.
4. A **predefined delay** is introduced using **Timer1** (CTC mode) to regulate the inflation process.
5. The system then **reduces the PWM duty cycle**, allowing controlled deflation of the airbag.
6. The process repeats as per sensor input.

---
## Code Explanation
### 1. **PWM Generation using Timer0**
- Timer0 operates in **Fast PWM Mode** on **Channel B (OC0B - PD5)**.
- The duty cycle changes dynamically to control the inflation speed.
- `OCR0B` is updated based on an increasing/decreasing pattern.

```c
void Timer0_init(void) {
    DDRD |= (1 << PORTD5);
    TCCR0A = (1 << WGM00) | (1 << WGM01) | (1 << COM0B1);
    TCCR0B = (1 << CS01) | (1 << CS00);
    OCR0B = duty_values[duty_index];
}
```

### 2. **Interrupt-based Delay using Timer1**
- Timer1 operates in **CTC mode** with a **prescaler of 256**.
- `OCR1A` is set for **1ms delay**, and `timer_count` is incremented inside the ISR.
- This ensures **non-blocking execution** of the system.

```c
ISR(TIMER1_COMPA_vect) {
    timer_count++;
}
```

### 3. **Touchpad-Based Activation**
- The touchpad input is connected to **PD2**, with an internal pull-up resistor enabled.
- The system waits for an active-low signal (`0V`) to initiate PWM control.

```c
bool is_touchpad_active(void) {
    return !(PIND & (1 << PIND1));
}
```

### 4. **Dynamic Duty Cycle Adjustment**
- The **duty cycle** increases until it reaches `100%` and then **decreases** back to `10%`.
- A **variable delay** is introduced to manage the inflation-deflation cycle.

```c
void process_pwm_duty_cycle(void) {
    if (increasing) {
        duty_index++;
        if (duty_index >= 10) {
            duty_index = 9;
            increasing = false;
        }
    } else {
        duty_index--;
        if (duty_index == 0) {
            increasing = true;
        }
    }
    Timer0_set_duty_cycle_channelB(duty_values[duty_index]);
    Timer1_delay_ms(increasing ? 200 : 400);
}
```

### 5. **Main Function**
- Initializes **PWM, Timer1, and Touchpad**.
- Enters an infinite loop where it continuously updates the PWM duty cycle based on sensor input.

```c
int main(void) {
    Touchpad_init();
    Timer1_init();

    if (!is_touchpad_active()) {
        Timer0_init();
        while (1) {
            process_pwm_duty_cycle();
        }
    }
}
```

---
## How to Build and Upload to AVR
### 1. **Compile the Code**
Use **AVR-GCC** to compile the source files:
```sh
avr-gcc -mmcu=atmega328p -DF_CPU=16000000UL -Os -c main.c -o main.o
```

### 2. **Link and Generate HEX File**
```sh
avr-gcc -mmcu=atmega328p -o airbag_system.elf main.o
avr-objcopy -O ihex airbag_system.elf airbag_system.hex
```

### 3. **Flash to Microcontroller**
Use **AVRDUDE** to upload the HEX file to the ATmega328P:
```sh
avrdude -c usbasp -p atmega328p -U flash:w:airbag_system.hex:i
```

### 4. **Verify Execution**
- Connect the **touchpad sensor** and observe PWM on **PD5 (OC0B)**.
- Check LED status or connect a **PWM-controlled actuator**.
- Use an **oscilloscope** to monitor the PWM signal variations.

---
## Troubleshooting
| Issue | Solution |
|--------|----------|
| No response from the system | Check power and clock configuration |
| PWM signal not changing | Verify `OCR0B` updates and timer settings |
| Touchpad not responding | Check pull-up resistor and wiring |
| Flashing error | Ensure correct **fuse bits** and programmer selection |

---
## Future Enhancements
- **Pressure Sensor Integration** for real-time impact detection.
- **Wireless Communication** for remote airbag control.
- **LCD Display** for system status monitoring.
- **Data Logging** for crash event recording.

---
## Conclusion
This **Automatic Air Bag Release System** effectively demonstrates a **PWM-based inflation mechanism** controlled via a **touchpad sensor**. By utilizing **timers and interrupts**, the system ensures **precise and smooth** airbag deployment. Future improvements can enhance safety and reliability for real-world applications.

---
## License
This project is open-source and available for modification under the **MIT License**.

