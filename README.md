# STM32 CMSIS-RTOS2 Implementation

## Overview
This project demonstrates the use of CMSIS-RTOS2 on an STM32F4 microcontroller, implementing various synchronization and scheduling techniques. The functionalities include:

1. **Counting Semaphore with Four Tasks**
2. **Binary Semaphore with Two Tasks**
3. **Queue-based Task Execution using Interrupts**
4. **Preemptive Scheduling**
5. **Round Robin Scheduling**

## Prerequisites
Before flashing the code to the STM32 board, ensure you have the following:

- **STM32CubeIDE** installed
- **CMSIS-RTOS2 library** included in your project
- **STM32F446RE** or a compatible board
- **Tera Term or PuTTY** for UART communication

## Project Implementation Details
### 1. Counting Semaphore with Four Tasks
- **Task1 and Task3:** Turn LED ON and print "LED ON" via UART
- **Task2 and Task4:** Turn LED OFF and print "LED OFF" via UART
- Uses a counting semaphore (max count 2) to control task execution

### 2. Binary Semaphore with Two Tasks
- **Task1:** LED ON and prints "Task1 Executing"
- **Task2:** LED OFF and prints "Task2 Executing"
- Synchronization is handled using a binary semaphore

### 3. Queue-based Task Execution using Interrupts
- **Task1:** LED ON for 15s, prints message from queue
- **Task2:** LED OFF for 15s, prints message from queue
- **Task3:** LED toggles for 15s, prints message from queue
- After execution, prints "Queue is empty. Process finished."

### 4. Preemptive Scheduling
- **Priority-based execution** where higher-priority tasks preempt lower-priority ones
- Implemented using CMSIS-RTOS2 thread priorities

### 5. Round Robin Scheduling
- **Equal time slices** for each task
- Tasks run cyclically in a time-sliced manner

## Configuration & Execution on STM32 Board
### 1. Hardware Setup
- Connect an **LED** to GPIO pin **PA5** (if using STM32F446RE)
- Connect the board to a PC via **USB**

### 2. Software Configuration
- Open **STM32CubeIDE** and create a new project
- Enable **CMSIS-RTOS2** middleware in CubeMX
- Enable **USART2 (UART)** for debugging
- Configure **GPIOA PIN 5** as output for LED control

### 3. Compiling & Flashing
- Build the project in STM32CubeIDE
- Connect the STM32 board via ST-Link
- Flash the firmware onto the microcontroller

### 4. Viewing Output
- Open **Tera Term/Putty**
- Set **Baud Rate: 115200**, **COM Port: Check from Device Manager**
- Observe task execution messages in the serial monitor

## Expected Output
```
Task1: LED ON
Task2: LED OFF
Task3: LED TOGGLE
Queue is empty. Process finished.
```

## Troubleshooting
- **Access Denied Error on COM Port:** Close other serial monitor applications
- **No UART Output:** Ensure proper UART configuration and check wiring
- **LED Not Blinking:** Verify GPIO configurations in CubeMX

## Conclusion
This project showcases how to implement multiple scheduling and synchronization techniques using **CMSIS-RTOS2** on an **STM32 board**. By following the above steps, you should be able to flash and execute the code successfully.

