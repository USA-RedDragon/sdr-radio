# SDR Radio Project

Subject to rename when a better one comes to me :)

## Project Goals

- Create a radio transceiver in the H/T format that can operate any mode with the appropriate software
- Create reusable design blocks for use in later RF projects
- Evaluate high-bandwidth IQ modems for HAM radio usage

## Targeted Specifications

- Dual-core MCU to allow true multitasking
- Ability to record raw RF data for later processing
- SD Card support for codeplug, advanced configuration, debugging, and sample recording
- A high enough resolution display to show a waterfall
- USB-C debugging, programming
- "headless" operation controlled via USB connection
- 100-1000MHz operating frequency range
- Up to 8W of RF output power, with levels from 0.5, 1, 2, 4, 5, and 8 watts
- 45MHz demodulation bandwidth
- Backlit keypad

## Project Status

Early stages of part selection of viability analysis.

## Block Diagram

```mermaid
graph BT;
    A[Antenna] -- IF --> Demodulator;
    Amp[RF Amplifier] --> A[Antenna];
    subgraph Logic
        MCU -- Baseband --> DAC;
        ADC -- Baseband --> MCU;
    end
    subgraph RX
        Demodulator -- Mixed --> RFDET[RF Detector];
        RFDET[RF Detector] -- AGC --> Demodulator;
        Demodulator -- I/Q --> ADC;
    end
    subgraph TX
        DAC -- I/Q --> Modulator;
        Modulator --> Amp[RF Amplifier]
    end
    subgraph Frequency Synthesis
        PLL/VCO -- LO*2 --> Demodulator;
        PLL/VCO -- LO --> Modulator;
    end
    subgraph Peripherals
        Keypad --> MCU;
        MCU -- MIPI DSI --> Display;
        MCU <--> USB[USB C];
    end
    subgraph Storage
        MCU --> SD[SD Card];
        MCU --> Flash;
    end
    subgraph Audio
        MCU --> AudioAmp[Amplifier];
        AudioAmp[Amplifier] --> Speaker;
        Microphone --> MCU;
    end
    USB[USB C] -. TX .-> Modulator;
    USB[USB C] -. RX .-> Demodulator;
    USB[USB C] -. Codeplug .-> SD[SD Card];
    USB[USB C] -. Baseband Samples .-> SD[SD Card];
    USB[USB C] -. Audio Samples .-> SD[SD Card];
    USB[USB C] -. Configuration .-> Flash;
    USB[USB C] -. System Updates .-> Flash;
    subgraph Power
        Battery -- 8.4V --> SMPS;
        SMPS -. 3.3V .-> MCU;
        SMPS -. 3.3V .-> PLL/VCO;
        SMPS -. 5V .-> Demodulator;
        SMPS -. 5V .-> USB[USB C];
    end
```

## Subprojects

- [`breakouts/ad8348-rf-demodulator`](https://github.com/USA-RedDragon/ad8348-breakout) - This board is aimed to evaluate the Analog Devices AD8348 for use as an ultra-wideband SDR receiver.
- [`breakouts/STM32H757IIT6-breakout`](https://github.com/USA-RedDragon/STM32H757IIT6-breakout) - This board is a breakout for the STM32H757IIT6 MCU.
