
# Homework 2
//by LI Xinwei, SID:20945660

//Question1
volatile uint32_t last_time = 0;
while(1){
    if(gpio_read(BTN1)){
        if(HAL_GetTick() - last_time >= 50){
            last_time = HAL_GetTick();
            gpio_toggle(LED2);
        }
    }
    else{
        led_off(LED2);
        tft_prints(0, 0, "Hello, Tina");
    }
    tft_update(10);
}


//Question2
volatile uint32_t last_time = 0;
volatile uint32_t start_click = 0;
volatile uint32_t stop_click = 0;
int last_state = 0;
while(1){
    if(!gpio_read(BTN1)){ //若BTN1被按下
        if(last_state == 0){
            start_click = HAL_GetTick();
            last_state = 1;
        }

    }
    else{
        if(last_state == 1){
            stop_click = HAL_GetTick();
            last_state = 0;
        }

    }
    if(HAL_GetTick() - start_click <= 1000){
        tft_prints(0, 0, "Hello, Tina");
    }
    if(HAL_GetTick() - stop_click <= 1000){
        if(HAL_GetTick() - last_time >= 50){
            last_time = HAL_GetTick();
            gpio_toggle(LED2);
        }
    }
    else{
        led_off(LED2);
    }
    tft_update(10);
}
    
    
//bonus
volatile uint32_t start_click_BTN1 = 0;
volatile uint32_t start_click_BTN2 = 0;
int state_BTN1 = 0;
int state_BTN2 = 0;
int x1 = 64, y1 = 80, w1 = 10, h1 = 5;
int x2 = 69, y2 = 70, w2 = 5, h2 = 10;
tft_print_rectangle(PINK, x1, y1, w1, h1);
tft_print_rectangle(PINK, x2, y2, w2, h2);
while(1){
    if(!gpio_read(BTN1)){//若BTN1被按下
        state_BTN1 = 1;
    }
    if(gpio_read(BTN1)){ //若BTN1没被按下
        if(state_BTN1 == 1){
            tft_force_clear();
            if(HAL_GetTick() - start_click_BTN1 >= 300){
                y1 -= CHAR_HEIGHT;
                y2 -= CHAR_HEIGHT;
                tft_print_rectangle(PINK, x1, y1, w1, h1);
                tft_print_rectangle(PINK, x2, y2, w2, h2);
            }
            else{
                x1 -= CHAR_WIDTH;
                x2 -= CHAR_WIDTH;
                tft_print_rectangle(PINK, x1, y1, w1, h1);
                tft_print_rectangle(PINK, x2, y2, w2, h2);
            }
        }
        start_click_BTN1 = HAL_GetTick();
        state_BTN1 = 0;
    }
    if(!gpio_read(BTN2)){//若BTN2被按下
        state_BTN2 = 1;
    }
    if(gpio_read(BTN2)){ //若BTN2没被按下
        if(state_BTN2 == 1){
            tft_force_clear();
            if(HAL_GetTick() - start_click_BTN2 >= 300){
                y1 += CHAR_HEIGHT;
                y2 += CHAR_HEIGHT;
                tft_print_rectangle(PINK, x1, y1, w1, h1);
                tft_print_rectangle(PINK, x2, y2, w2, h2);
            }
            else{
                x1 += CHAR_WIDTH;
                x2 += CHAR_WIDTH;
                tft_print_rectangle(PINK, x1, y1, w1, h1);
                tft_print_rectangle(PINK, x2, y2, w2, h2);
            }
        }
        start_click_BTN2 = HAL_GetTick();
        state_BTN2 = 0;
    }
    tft_update(10);
}
    

//Complete one
// Tutorial2 main.c

/* USER CODE BEGIN Header */
/**
 ******************************************************************************
 * @file           : main.c
 * @brief          : Main program body
 ******************************************************************************
 * @attention
 *
 * Copyright (c) 2022 STMicroelectronics.
 * All rights reserved.
 *
 * This software is licensed under terms that can be found in the LICENSE file
 * in the root directory of this software component.
 * If no LICENSE file comes with this software, it is provided AS-IS.
 *
 ******************************************************************************
 */
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "can.h"
#include "dma.h"
#include "i2c.h"
#include "spi.h"
#include "tim.h"
#include "usart.h"
#include "gpio.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include "lcd/lcd.h"
/* USER CODE END Includes */

/* Private typedef -----------------------------------------------------------*/
/* USER CODE BEGIN PTD */

/* USER CODE END PTD */

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */
/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */

/* USER CODE END PM */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */

/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */

/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */

/* USER CODE END 0 */

/**
 * @brief  The application entry point.
 * @retval int
 */
int main(void) {
    /* USER CODE BEGIN 1 */

    /* USER CODE END 1 */

    /* MCU Configuration--------------------------------------------------------*/

    /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
    HAL_Init();

    /* USER CODE BEGIN Init */

    /* USER CODE END Init */

    /* Configure the system clock */
    SystemClock_Config();

    /* USER CODE BEGIN SysInit */

    /* USER CODE END SysInit */

    /* Initialize all configured peripherals */
    MX_GPIO_Init();
    MX_CAN1_Init();
    MX_CAN2_Init();
    MX_USART1_UART_Init();
    MX_DMA_Init();
    MX_USART2_UART_Init();
    MX_TIM5_Init();
    /* USER CODE BEGIN 2 */
    volatile uint32_t last_ticks = 0;

    // we turn off all the led first
    led_off(LED1);
    led_off(LED2);
    led_off(LED3);
    led_off(LED4);
    tft_init(PIN_ON_TOP, BLACK, WHITE, YELLOW, DARK_GREEN);
    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_3, GPIO_PIN_SET);

    /* USER CODE END 2 */

    /* Infinite loop */
    /* USER CODE BEGIN WHILE */
    tft_force_clear();

    
    //Question1
    volatile uint32_t last_time = 0;
    while(1){
    	if(gpio_read(BTN1)){
    		if(HAL_GetTick() - last_time >= 50){
    			last_time = HAL_GetTick();
    			gpio_toggle(LED2);
    		}
    	}
    	else{
    		led_off(LED2);
    		tft_prints(0, 0, "Hello, Tina");
    	}
    	tft_update(10);
    }
    
    //Question2
    volatile uint32_t last_time = 0;
    volatile uint32_t start_click = 0;
    volatile uint32_t stop_click = 0;
    int last_state = 0;
    while(1){
    	if(!gpio_read(BTN1)){ //若BTN1被按下
    		if(last_state == 0){
    			start_click = HAL_GetTick();
    			last_state = 1;
    		}

    	}
    	else{
    		if(last_state == 1){
    			stop_click = HAL_GetTick();
    			last_state = 0;
    		}

    	}
    	if(HAL_GetTick() - start_click <= 1000){
    		tft_prints(0, 0, "Hello, Tina");
    	}
    	if(HAL_GetTick() - stop_click <= 1000){
    		if(HAL_GetTick() - last_time >= 50){
				last_time = HAL_GetTick();
				gpio_toggle(LED2);
			}
    	}
    	else{
    		led_off(LED2);
    	}
    	tft_update(10);
    }

    //bonus
    volatile uint32_t start_click_BTN1 = 0;
    volatile uint32_t start_click_BTN2 = 0;
    int state_BTN1 = 0;
    int state_BTN2 = 0;
    int x1 = 64, y1 = 80, w1 = 10, h1 = 5;
    int x2 = 69, y2 = 70, w2 = 5, h2 = 10;
    tft_print_rectangle(PINK, x1, y1, w1, h1);
    tft_print_rectangle(PINK, x2, y2, w2, h2);
    while(1){
    	if(!gpio_read(BTN1)){//若BTN1被按下
    	    state_BTN1 = 1;
    	}
    	if(gpio_read(BTN1)){ //若BTN1没被按下
    		if(state_BTN1 == 1){
    			tft_force_clear();
    			if(HAL_GetTick() - start_click_BTN1 >= 300){
    				y1 -= CHAR_HEIGHT;
    				y2 -= CHAR_HEIGHT;
					tft_print_rectangle(PINK, x1, y1, w1, h1);
					tft_print_rectangle(PINK, x2, y2, w2, h2);
				}
    			else{
    				x1 -= CHAR_WIDTH;
    				x2 -= CHAR_WIDTH;
    				tft_print_rectangle(PINK, x1, y1, w1, h1);
    				tft_print_rectangle(PINK, x2, y2, w2, h2);
    			}
    		}
			start_click_BTN1 = HAL_GetTick();
			state_BTN1 = 0;
		}
    	if(!gpio_read(BTN2)){//若BTN1被按下
			state_BTN2 = 1;
		}
		if(gpio_read(BTN2)){ //若BTN1没被按下
			if(state_BTN2 == 1){
				tft_force_clear();
				if(HAL_GetTick() - start_click_BTN2 >= 300){
					y1 += CHAR_HEIGHT;
					y2 += CHAR_HEIGHT;
					tft_print_rectangle(PINK, x1, y1, w1, h1);
					tft_print_rectangle(PINK, x2, y2, w2, h2);
				}
				else{
					x1 += CHAR_WIDTH;
					x2 += CHAR_WIDTH;
					tft_print_rectangle(PINK, x1, y1, w1, h1);
					tft_print_rectangle(PINK, x2, y2, w2, h2);
				}
			}
			start_click_BTN2 = HAL_GetTick();
			state_BTN2 = 0;
		}
		tft_update(10);
    }

    
}

/**
 * @brief System Clock Configuration
 * @retval None
 */
void SystemClock_Config(void) {
    RCC_OscInitTypeDef RCC_OscInitStruct = { 0 };
    RCC_ClkInitTypeDef RCC_ClkInitStruct = { 0 };

    /** Configure the main internal regulator output voltage
     */
    __HAL_RCC_PWR_CLK_ENABLE();
    __HAL_PWR_VOLTAGESCALING_CONFIG(PWR_REGULATOR_VOLTAGE_SCALE1);

    /** Initializes the RCC Oscillators according to the specified parameters
     * in the RCC_OscInitTypeDef structure.
     */
    RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSE;
    RCC_OscInitStruct.HSEState = RCC_HSE_ON;
    RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
    RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSE;
    RCC_OscInitStruct.PLL.PLLM = 4;
    RCC_OscInitStruct.PLL.PLLN = 168;
    RCC_OscInitStruct.PLL.PLLP = RCC_PLLP_DIV2;
    RCC_OscInitStruct.PLL.PLLQ = 4;
    if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK) {
        Error_Handler();
    }

    /** Initializes the CPU, AHB and APB buses clocks
     */
    RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK | RCC_CLOCKTYPE_SYSCLK
            | RCC_CLOCKTYPE_PCLK1 | RCC_CLOCKTYPE_PCLK2;
    RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
    RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
    RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV4;
    RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV2;

    if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_5) != HAL_OK) {
        Error_Handler();
    }
}

/* USER CODE BEGIN 4 */

/* USER CODE END 4 */

/**
 * @brief  This function is executed in case of error occurrence.
 * @retval None
 */
void Error_Handler(void) {
    /* USER CODE BEGIN Error_Handler_Debug */
    /* User can add his own implementation to report the HAL error return state */
    __disable_irq();
    while (1) {
    }
    /* USER CODE END Error_Handler_Debug */
}

#ifdef  USE_FULL_ASSERT
/**
  * @brief  Reports the name of the source file and the source line number
  *         where the assert_param error has occurred.
  * @param  file: pointer to the source file name
  * @param  line: assert_param error line source number
  * @retval None
  */
void assert_failed(uint8_t *file, uint32_t line)
{
  /* USER CODE BEGIN 6 */
  /* User can add his own implementation to report the file name and line number,
     ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */
  /* USER CODE END 6 */
}
#endif /* USE_FULL_ASSERT */

