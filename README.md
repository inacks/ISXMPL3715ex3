# Example Reference Code: ISXMPL3715ex3

## Objective
Demonstrate how to write a program for the **IS3715 I²C DMX Controller** using **STM32 CubeIDE**. 

## Required Material
- **Kappa3715Ard Board** (INACKS evaluation board featuring the IS3715)  
- **STM32 Nucleo-C071RB**  
- **DMX-compatible light fixture**  

## Setup
Download the **ISXMPL3715ex3** from [https://inacks.com/is3715](https://inacks.com/is3715) and flash it to the **Nucleo-C071RB Board**.  
Connect the Kappa3715 to the Nucleo-C071RB, and attach a DMX light to validate its operation.  

## How it works
The CubeIDE code continuously increments the variable `dmxChannel1Value` by 1.  
This value is written to **register address 1 of the IS3715**, which causes the DMX channel value to increase continuously.  

## Other
- Download the example code: [https://inacks.com/is3715](https://inacks.com/is3715)  
- Product information (Kappa3715Ard): [https://inacks.com/kappa3715ard](https://inacks.com/kappa3715ard)

---

⚠️ **Attention**  
The following code is an extraction of the relevant parts of the CubeIDE project.  
Pasting this code directly into CubeIDE will **not** work, as all the CubeIDE libraries and extra functions from its HAL libraries are omitted for pedagogical purposes.  
Download the full CubeIDE project or paste this code into the appropriate sections of a CubeIDE project.  

```c
#include <stdio.h>

// IS3715 Memory Map Registers:
#define CHIP_ID 	513
#define CHIP_REV 	514

// This variable stores the data of the DMX Channel 1:
uint8_t dmxChannel1Value;

/**
 * @brief	This routine reads a single register from the IS3715 memory map.
 * @param	registerAdressToRead: Address in the IS3715 memory map to be read.
 * @retval	Value stored at the registerAdressToRead register address.
 */
uint8_t readIS3715Register(uint16_t registerAdressToRead) {
	uint8_t readResult = 0;
	uint8_t IS3715_I2C_Chip_Address;
    IS3715_I2C_Chip_Address = 0x15; // IS3715 I2C address is 0x15 (7-bit).
    // The STM32 HAL I2C library requires the I2C address to be shifted left by one bit.
    // Let's shift the IS3715 I2C address accordingly:
    IS3715_I2C_Chip_Address = IS3715_I2C_Chip_Address << 1;

    HAL_I2C_Mem_Read(&hi2c1, IS3715_I2C_Chip_Address, registerAdressToRead, I2C_MEMADD_SIZE_16BIT, &readResult, 1, 500);

    return readResult;
}

/**
 * @brief	Reads a single register fro*
