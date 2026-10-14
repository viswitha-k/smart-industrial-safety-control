# 🏭 Smart Industrial Safety Control

A microcontroller-based industrial safety monitoring and control prototype developed using the **STM32F103RB** microcontroller and STM32 HAL library. The project demonstrates sensor monitoring, emergency-stop handling, status indication, relay control, motor-control outputs, and timer-based safety-state updates.

## 📌 Project Overview

Industrial environments often contain moving machinery and equipment that can create hazardous situations for workers. A safety monitoring system can help detect abnormal conditions and initiate an appropriate control response.

The Smart Industrial Safety Control project uses an STM32 microcontroller to monitor an infrared (IR) sensor input and an emergency push button. The firmware maintains a safety state and controls output signals based on the detected condition.

The project is developed in **C using STM32CubeIDE and STM32 HAL**, with GPIO and timer peripherals configured for embedded control.

> **Note:** This is an educational prototype, not a certified industrial safety system. It must not be used as the sole protective mechanism for real industrial machinery.

## 🎯 Objectives

- Monitor an industrial safety-related condition using an IR sensor.
- Handle an emergency push-button input through an external interrupt.
- Represent system operation using NORMAL, WARNING, and EMERGENCY states.
- Control an LED indicator to communicate system status.
- Provide relay and motor-control output signals.
- Use STM32 timer peripherals for PWM, input capture, and periodic interrupt handling.
- Establish a foundation for future industrial safety monitoring enhancements.

## ⚙️ Hardware Components

| Component | Purpose |
|---|---|
| STM32F103RB microcontroller | Main embedded processing unit |
| IR sensor module | Detects an object or condition within its sensing range |
| Emergency push button | Triggers the emergency-state handler |
| LED indicator | Provides visual status indication |
| Relay interface | Provides a control signal for a relay driver |
| Motor-control interface | Provides motor direction/control signals |
| Supporting circuitry | Power supply, wiring, driver circuits, and protection |

**Additional hardware may be required** depending on the selected sensor, relay, motor, and development board.

## 💻 Software Requirements

- STM32CubeIDE
- STM32CubeMX configuration support
- C programming language
- STM32F1 HAL drivers
- ST-LINK debugger/programmer or another compatible programming interface

## 🧠 System Architecture

```text
       IR Sensor Input
              |
              v
     STM32F103RB MCU
              |
       Safety State Logic
              |
       +------+------+
       |      |      |
       v      v      v
      LED   Relay   Motor
              ^
              |
    Emergency Push Button
       (External Interrupt)
```

The firmware initializes the microcontroller, GPIO pins, and timer peripherals. A periodic TIM4 interrupt invokes the safety-state update function. The emergency push button uses an external interrupt callback to request the EMERGENCY state and immediately update the associated output signals within the callback.

## 🔄 Safety States

### 1. NORMAL

- The IR sensor does not indicate the condition that triggers a warning.
- The firmware sets the current state to `NORMAL`.
- The LED indicator is switched off.

### 2. WARNING

- The IR sensor input is detected as active.
- The firmware sets the current state to `WARNING`.
- The LED indicator is switched on.

### 3. EMERGENCY

- The emergency push button triggers the external interrupt callback.
- The firmware sets the current state to `EMERGENCY`.
- The LED indicator is switched on.
- The relay output is reset.
- Both motor-control outputs are reset.

**Important implementation detail:** The supplied firmware keeps the system in the EMERGENCY state when the periodic safety update runs. A deliberate reset or recovery mechanism still needs to be implemented. The exact physical effect of the relay and motor signals depends on the connected driver circuitry.

## 🔌 Peripheral Configuration

The firmware configures the following STM32 peripherals:

| Peripheral | Configuration / Purpose |
|---|---|
| GPIO | IR sensor input, push-button interrupt, LED, relay, and motor-control outputs |
| TIM2 | PWM generation on Channel 1 |
| TIM3 | Input-capture configuration on Channel 1 |
| TIM4 | Base timer with periodic interrupt enabled |
| EXTI | External interrupt handling for the emergency push button |

### Timer Details

- **TIM2:** Prescaler = 71, period = 999, PWM pulse = 0.
- **TIM3:** Prescaler = 7, period = 65535, rising-edge input capture.
- **TIM4:** Prescaler = 7999, period = 999, periodic interrupt enabled.

The actual timer frequencies and interrupt intervals depend on the effective timer clock. Verify the clock configuration and hardware pin mapping before calculating or reporting timing values.

## 📁 Project Structure

The main source files in the STM32CubeIDE project include:

```text
Smart-Industrial-Safety-Control/
├── Core/
│   ├── Inc/
│   │   ├── main.h
│   │   ├── stm32f1xx_hal_conf.h
│   │   └── stm32f1xx_it.h
│   └── Src/
│       ├── main.c
│       ├── stm32f1xx_hal_msp.c
│       ├── stm32f1xx_it.c
│       ├── system_stm32f1xx.c
│       ├── syscalls.c
│       └── sysmem.c
├── Drivers/
│   ├── CMSIS/
│   └── STM32F1xx_HAL_Driver/
├── startup_stm32f103rbtx.s
├── smart industrial safety control.ioc
└── README.md
```

*The directory names above describe the typical STM32CubeIDE layout. Keep the actual folder structure and generated filenames from your downloaded project when uploading to GitHub.*

## 🚀 Getting Started

### Step 1: Clone the Repository

```bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
```

Replace the URL with your actual GitHub repository URL.

### Step 2: Open the Project

1. Launch STM32CubeIDE.
2. Select **File → Import → Existing Projects into Workspace**.
3. Browse to the cloned project directory.
4. Select the project and complete the import.

### Step 3: Check the Configuration

- Open the `.ioc` configuration file.
- Verify the selected MCU and clock configuration.
- Review GPIO assignments for the IR sensor, emergency button, LED, relay, and motor outputs.
- Verify timer channels and their corresponding physical pins.
- Confirm that the external interrupt is configured correctly.

### Step 4: Build the Firmware

1. Select the project in STM32CubeIDE.
2. Choose **Project → Build Project**.
3. Resolve any build errors or missing dependencies.
4. Review compiler warnings before proceeding.

### Step 5: Flash and Debug

1. Connect the compatible STM32 development board.
2. Connect the ST-LINK programmer/debugger.
3. Select **Run** or **Debug** in STM32CubeIDE.
4. Verify the firmware behavior using the debugger and appropriate test equipment.

**Do not connect motors or other high-power industrial loads directly to microcontroller GPIO pins.** Use correctly rated driver circuitry, isolation, and electrical protection.

## 🧪 Testing and Validation

The following tests are recommended before considering the prototype functional:

| Test | Expected behavior |
|---|---|
| Power-on | MCU initializes the configured peripherals |
| IR sensor inactive | State becomes NORMAL and LED is off |
| IR sensor active | State becomes WARNING and LED is on |
| Emergency button pressed | State becomes EMERGENCY and LED is on |
| Emergency condition | Relay and motor-control output signals are reset |
| TIM2 PWM | Verify the output with a suitable logic analyzer or oscilloscope |
| TIM4 interrupt | Confirm periodic callback execution |
| Emergency recovery | Verify that recovery requires an intentional reset procedure |

These are expected behaviors for validation, not a claim that all tests have been completed. Actual results should be recorded after testing on the target hardware.

## ⚠️ Current Limitations

- The supplied firmware provides the main peripheral initialization and safety-state handling foundation.
- The IR sensor's behavior depends on its module, signal polarity, wiring, and sensing conditions.
- The emergency-state recovery/reset process needs further development.
- The PWM output is initialized with a pulse value of zero; the required motor-control behavior needs further implementation and validation.
- Sensor fault detection, input debouncing, watchdog handling, and fault logging need additional work.
- No verified hardware test measurements or reliability results are included in the supplied source files.

## 🔮 Future Enhancements

- Add a deliberate emergency reset and recovery procedure.
- Implement sensor-disconnection detection and fault handling.
- Add switch debouncing and noise filtering.
- Integrate suitable industrial sensors for temperature, gas, vibration, or restricted-zone monitoring.
- Add event logging and a local display or monitoring dashboard.
- Implement watchdog-based recovery and startup diagnostics.
- Add hardware interlocks and independent emergency-stop circuitry where required.
- Conduct systematic fault-injection testing and a formal safety assessment before any real-world deployment.

## 📚 References

- STMicroelectronics — [STM32F103RB Product Documentation](https://www.st.com/en/microcontrollers-microprocessors/stm32f103rb.html)
- STMicroelectronics — [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- STMicroelectronics — [STM32CubeF1 Firmware Package](https://www.st.com/en/embedded-software/stm32cubef1.html)
- IEC 60204-1 — Safety of machinery: Electrical equipment of machines.
- ISO 13850 — Safety of machinery: Emergency stop function.

Consult the applicable editions and qualified safety guidance when designing systems for actual industrial use.

## 👥 Project Information

- **Project Title:** Smart Industrial Safety Control
- **Domain:** Embedded Systems / Industrial Safety
- **Microcontroller:** STM32F103RB
- **Programming Language:** C
- **Development Environment:** STM32CubeIDE
- **Project Type:** Academic Project Based Learning (PBL)

### Contributors


- Contributor 1 —  viswitha 2510080015
- Contributor 2 —  akshaya 2510080024
- Contributor 3 —  shruthi 2510080028
- contributor 4 -  akhil 2510080009
- contributor 5 -  shravani 2510080065
- contributor 6 -  jeshwanth 2510080047
---

⭐ If you find this project useful for learning embedded systems and microcontroller-based control, feel free to star the repository.
