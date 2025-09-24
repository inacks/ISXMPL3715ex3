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
 * @brief	Reads a single register from the IS3715 memory map.
 * @param	registerAdressToRead: Address in the IS3715 memory map to be read.
 * @retval	Value stored at the registerAdressToRead register address.
 */
void writeIS3715Register(uint16_t registerAdressToWrite, uint8_t value) {
    uint8_t IS3715_I2C_Chip_Address;  // I2C address of IS3715 chip (7-bit).
    IS3715_I2C_Chip_Address = 0x15; // IS3715 I2C address is 0x15 (7-bit).
    // STM32 HAL expects 8-bit address, so shift left by 1:
    IS3715_I2C_Chip_Address = IS3715_I2C_Chip_Address << 1;
	
    HAL_I2C_Mem_Write(&hi2c1, IS3715_I2C_Chip_Address, registerAdressToWrite, I2C_MEMADD_SIZE_16BIT, &value, 1, 500);
}

// This function is required to make printf work over the Serial port.
// It is never called directly in the code — printf internally uses it
int _write(int file, char *ptr, int len) {
	HAL_UART_Transmit(&huart2, (uint8_t*)ptr, len, HAL_MAX_DELAY);
    return len;
}

int main(void) {
	HAL_StatusTypeDef chipDetected = HAL_I2C_IsDeviceReady(&hi2c1, (0x15 << 1), 3, 200);

	if (chipDetected == HAL_OK) {
	  printf("IS3715 Detected!\n");
	}
	else {
	  printf("ERROR: IS3715 not detected. Program stops now.\n");
	  while(1);
	}
	
	while (1) {
	  writeIS3715Register(1, dmxChannel1Value);
	  dmxChannel1Value++;
	  HAL_Delay(10);
	}
}
