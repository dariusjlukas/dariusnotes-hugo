---
title: "Blink"
date: 2020-07-31T18:26:46-04:00
draft: true
---

# ESP32 Getting Started - Blink
{{< gimg "https://drive.google.com/file/d/1yYD6ve40sl5sgrEQ78U8eDRd08tnLRzG/preview" 570 540>}}

## Background
The ESP32 line of modules by Espressif contain a dual-core, 32-bit processor capable of clock speeds of up to 240 MHz. The module also 
includes WiFi, Bluetooth, and hardware accelerators for a variety of peripherals. Programming the ESP32 can be done via
the Arduino IDE, Espressif's IDF (Integrated Development Framework), or a third-party toolchain. While the Arduino IDE is convenient, using the ESP-IDF enables the user to take full advantage of the dual-core nature of the ESP32, and of standard libraries that interact with peripherals. For these reasons, we will be using the ESP-IDF for this guide. Espressif's ESP-IDF documentation can be found {{< link "https://docs.espressif.com/projects/esp-idf/en/latest/esp32/index.html" here >}}, and the documentation for FreeRTOS, the barebones, real-time operating system used by ESP-IDF, can be found 
{{< link "https://www.freertos.org/RTOS.html" here >}}. The following guide will show you how to make an RGB LED blink periodically, taking advantage of basic FreeRTOS features and the ESP-IDF. To follow along, you will need an ESP32 development board with an RGB LED attached to pins 23, 5, and 0, however the pin assignments are easy to adjust in the code.

## Step 1: Get ESP-IDF
Installation instructions for installing ESP-IDF on Linux, Mac, and Windows can be found {{< link "https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html#get-started-get-esp-idf" here >}}. Once you get to the end of this guide, you should have ESP-IDF all set up, and be able to continue on to step 2.

## Step 2: Create a new project
ESP-IDF requires a particular directory structure in order to build projects properly. For this reason, it is often easiest to start a new project by copying an example project and modifying the main .c file. ESP-IDF comes with quite a few examples located in esp-idf/examples. For the sake of this tutorial, a template project can be found {{< link "https://drive.google.com/file/d/1nl-vIL4I5iRtSUm5sU0COSgtcNbDChu1/view?usp=sharing" here >}}. Download the compressed project directory, unzip, and open the file blink_tutorial/main/blink.c in your favorite editor (I will be using {{< link "https://code.visualstudio.com/" VSCode >}}) to get started. 

## Step 3: Include necessary libraries
A number of standard libraries must be included in order for this example to work. We will include stdio in order to have the ability to print information over the serial connection, FreeRTOS for system calls, and the gpio library to have access to the GPIO (General Purpose Input/Output) pins on the ESP32 in order to drive the LED. Add the following lines to the top of the blink.c file (blink_tutorial/main/blink.c):

{{< highlight C "linenos=table,linenostart=2">}}
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/gpio.h"
{{< / highlight >}}

In addition to including libraries, we will also create macros for the RGB LED pin assignments. On the dev board I am using, the pins used are GPIO 23, GPIO 0, and GPIO 5. These values can be changed depending on how you have your board wired. For the board I am using, I would add the lines:
{{< highlight C  "linenos=table,linenostart=7">}}
#define RED_CHANNEL 23
#define GREEN_CHANNEL 0
#define BLUE_CHANNEL 5
{{< / highlight >}}

Before we continue, we will also need to add a main function. Like most C and C++ environments, ESP-IDF uses a main function as the entry point into the program. ESP-IDF calls the function "app_main". Add the following lines to your blink.c file:
{{< highlight C  "linenos=table,linenostart=11">}}
void app_main()
{


}
{{< / highlight >}}

## Step 4: Setup GPIO pins
Before we can start using the GPIO pins, we must first configure the pins for output. The reason for this is that the ESP32 internally can use the same pin for multiple functions, so we must be explicit that these pins are intended to be used for GPIO output. The following lines will configure out previously defined red, green, and blue channels as outputs. Add them at the top of your app_main() function.
{{< highlight C "linenos=table,linenostart=14" >}}
    gpio_pad_select_gpio(RED_CHANNEL);
    gpio_pad_select_gpio(GREEN_CHANNEL);
    gpio_pad_select_gpio(BLUE_CHANNEL);
    
    gpio_set_direction(RED_CHANNEL, GPIO_MODE_OUTPUT);
    gpio_set_direction(GREEN_CHANNEL, GPIO_MODE_OUTPUT);
    gpio_set_direction(BLUE_CHANNEL, GPIO_MODE_OUTPUT);
{{< / highlight >}}

## Step 5: Turn the LED on and off
Now that the pins are configured, we can toggle them on and off in an infinite loop. Add the following while loop with the gpio_set_level() calls to toggle the pins high and low (1 and 0).
{{< highlight C "linenos=table,linenostart=22" >}}
while(1) {
        gpio_set_level(RED_CHANNEL, 0);
        gpio_set_level(GREEN_CHANNEL, 0);
        gpio_set_level(BLUE_CHANNEL, 0);
        
        gpio_set_level(RED_CHANNEL, 1);
        gpio_set_level(GREEN_CHANNEL, 1);
        gpio_set_level(BLUE_CHANNEL, 1);
    }
{{< / highlight >}}

## Step 6: Add task delays
Currently, our program will toggle the LED on and off. The problem is, it will toggle the LED on and off as fast as it can, which is too fast for the human eye to see. In order to slow down the toggling, we will add task delays. These task delays are similar to delays in Arduino with one key difference: they only block the block the current task. This is one of the major benefits to using an RTOS like FreeRTOS, is that we can take advantage of the concept of multiple tasks running in parallel, and delay one without pausing the rest. This advantage is not particularly evident right now (since we only have one task), but is a makes more complex code much simpler. To add one second delays, add the following lines after setting the LED pins high and low:
{{< highlight C "linenos=table,hl_lines=4 9,linenostart=22" >}}
while(1) {
        gpio_set_level(RED_CHANNEL, 0);
        gpio_set_level(GREEN_CHANNEL, 0);
        gpio_set_level(BLUE_CHANNEL, 0);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
        
        gpio_set_level(RED_CHANNEL, 1);
        gpio_set_level(GREEN_CHANNEL, 1);
        gpio_set_level(BLUE_CHANNEL, 1);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
{{< / highlight >}}

Notice that the vTaskDelay values are not 1000 milliseconds (1 second), but rather 1000 / portTICK_PERIOD_MS. This is because vTaskDelay() works with the concept of ticks. In order to translate tick into milliseconds, we divide the desired millisecond value by the macro portTICK_PERIOD_MS.

## Step 7: Add serial messages
In addition to blinking the LED, we can write messages over the serial terminal when we turn the LED on and when we turn the LED off. To do so, add the following lines before setting the GPIO levels on/off.
{{< highlight C "linenos=table,hl_lines=2 8,linenostart=22" >}}
while(1) {
	printf("Turning on the LED\n");
        gpio_set_level(RED_CHANNEL, 0);
        gpio_set_level(GREEN_CHANNEL, 0);
        gpio_set_level(BLUE_CHANNEL, 0);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
        
	printf("Turning off the LED\n");
        gpio_set_level(RED_CHANNEL, 1);
        gpio_set_level(GREEN_CHANNEL, 1);
        gpio_set_level(BLUE_CHANNEL, 1);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
{{< / highlight >}}

{{< expand note "..." >}}
The "Turning on the LED" and "Turning off the LED" messages might be backwards on your setup depending on if your RGB LED is common anode or common cathode.
{{< / expand >}}

The idf.py tool can be used to read the output of the serial terminal. On Linux, the command is: {{< cmd "$ idf.py monitor" >}}

## Step 8: Build and upload
The basic blink example is now complete and ready to be uploaded. The complete blink.c file should look like this:
{{< highlight C "linenos=table,linenostart=2" >}}

#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/gpio.h"

#define RED_CHANNEL 23
#define GREEN_CHANNEL 0
#define BLUE_CHANNEL 5

void app_main()
{
    
    gpio_pad_select_gpio(RED_CHANNEL);
    gpio_pad_select_gpio(GREEN_CHANNEL);
    gpio_pad_select_gpio(BLUE_CHANNEL);
    
    gpio_set_direction(RED_CHANNEL, GPIO_MODE_OUTPUT);
    gpio_set_direction(GREEN_CHANNEL, GPIO_MODE_OUTPUT);
    gpio_set_direction(BLUE_CHANNEL, GPIO_MODE_OUTPUT);

    while(1) {
	printf("Turning on the LED\n");
        gpio_set_level(RED_CHANNEL, 0);
        gpio_set_level(GREEN_CHANNEL, 0);
        gpio_set_level(BLUE_CHANNEL, 0);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
        
	printf("Turning off the LED\n");
        gpio_set_level(RED_CHANNEL, 1);
        gpio_set_level(GREEN_CHANNEL, 1);
        gpio_set_level(BLUE_CHANNEL, 1);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}
{{< / highlight >}}

The uploading method is the same as in the ESP-IDF install guide found {{< link "https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html#step-9-flash-onto-the-device" here >}}. On Linux, {{< cmd "$ idf.py flash" >}} should work. 

The results should be similar to what is depicted below:

{{< gimg "https://drive.google.com/file/d/1faFyozgQywtYTJwSWdTFq3OxBRNLxGBR/preview" 720 540 >}}
{{< gimg "https://drive.google.com/file/d/1zSAy8nfKellqHujGjhP9NI9rnV61Wtm0/preview" 720 540 >}}

## Step 9: Make it more interesting
The above example simply flashes the RGB LED on and off, i.e. all channels are synchronized. A more interesting demo would be to drive each channel individually. A simple method of doing so is shown below:
{{< highlight C "linenos=table,linenostart=22" >}}
while(1) {
        printf("All off\n");
        gpio_set_level(RED_CHANNEL, 1);
        gpio_set_level(GREEN_CHANNEL, 1);
        gpio_set_level(BLUE_CHANNEL, 1);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
        
        printf("Red channel on\n");
        gpio_set_level(RED_CHANNEL, 0);
        vTaskDelay(1000 / portTICK_PERIOD_MS);

        printf("Green channel on\n");
        gpio_set_level(GREEN_CHANNEL, 0);
        vTaskDelay(1000 / portTICK_PERIOD_MS);

        printf("Blue channel on\n");
        gpio_set_level(BLUE_CHANNEL, 0);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
{{< / highlight >}}

I would encourage you to experiment with different combinations of the channels and different methods of controlling them.

## Step 10: Hardware LED Driver

If you were to actually deploy a final product that used the esp32 to drive LEDs, chances are you will want to use the built-in LED driver. The ESP32's hardware LED driver is capable of changing the intensity of group of LEDs and handle gradual transitions between different intensities while using minimal cpu resources. The exact workings of the LED driver are beyond the scope of this tutorial, but documentation on the LED driver can be found {{< link "https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/ledc.html" here >}}, and below is a copy of example code provided by Espressif that takes advantage of the LED driver. The LED driver is just one of several hardware-implimented peripheral drivers included in the ESP32. A complete list of the ESP32's peripherals and documentation on them can be found {{< link "https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/index.html" here >}}.

{{< highlight C "linenos=table,linenostart=1" >}}
/* LEDC (LED Controller) fade example

   This example code is in the Public Domain (or CC0 licensed, at your option.)

   Unless required by applicable law or agreed to in writing, this
   software is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
   CONDITIONS OF ANY KIND, either express or implied.
*/
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/ledc.h"
#include "esp_err.h"

/*
 * About this example
 *
 * 1. Start with initializing LEDC module:
 *    a. Set the timer of LEDC first, this determines the frequency
 *       and resolution of PWM.
 *    b. Then set the LEDC channel you want to use,
 *       and bind with one of the timers.
 *
 * 2. You need first to install a default fade function,
 *    then you can use fade APIs.
 *
 * 3. You can also set a target duty directly without fading.
 *
 * 4. This example uses GPIO18/19/4/5 as LEDC output,
 *    and it will change the duty repeatedly.
 *
 * 5. GPIO18/19 are from high speed channel group.
 *    GPIO4/5 are from low speed channel group.
 *
 */
#ifdef CONFIG_IDF_TARGET_ESP32
#define LEDC_HS_TIMER          LEDC_TIMER_0
#define LEDC_HS_MODE           LEDC_HIGH_SPEED_MODE
#define LEDC_HS_CH0_GPIO       (18)
#define LEDC_HS_CH0_CHANNEL    LEDC_CHANNEL_0
#define LEDC_HS_CH1_GPIO       (19)
#define LEDC_HS_CH1_CHANNEL    LEDC_CHANNEL_1
#endif
#define LEDC_LS_TIMER          LEDC_TIMER_1
#define LEDC_LS_MODE           LEDC_LOW_SPEED_MODE
#ifdef CONFIG_IDF_TARGET_ESP32S2
#define LEDC_LS_CH0_GPIO       (18)
#define LEDC_LS_CH0_CHANNEL    LEDC_CHANNEL_0
#define LEDC_LS_CH1_GPIO       (19)
#define LEDC_LS_CH1_CHANNEL    LEDC_CHANNEL_1
#endif
#define LEDC_LS_CH2_GPIO       (4)
#define LEDC_LS_CH2_CHANNEL    LEDC_CHANNEL_2
#define LEDC_LS_CH3_GPIO       (5)
#define LEDC_LS_CH3_CHANNEL    LEDC_CHANNEL_3

#define LEDC_TEST_CH_NUM       (4)
#define LEDC_TEST_DUTY         (4000)
#define LEDC_TEST_FADE_TIME    (3000)

void app_main(void)
{
    int ch;

    /*
     * Prepare and set configuration of timers
     * that will be used by LED Controller
     */
    ledc_timer_config_t ledc_timer = {
        .duty_resolution = LEDC_TIMER_13_BIT, // resolution of PWM duty
        .freq_hz = 5000,                      // frequency of PWM signal
        .speed_mode = LEDC_LS_MODE,           // timer mode
        .timer_num = LEDC_LS_TIMER,            // timer index
        .clk_cfg = LEDC_AUTO_CLK,              // Auto select the source clock
    };
    // Set configuration of timer0 for high speed channels
    ledc_timer_config(&ledc_timer);
#ifdef CONFIG_IDF_TARGET_ESP32
    // Prepare and set configuration of timer1 for low speed channels
    ledc_timer.speed_mode = LEDC_HS_MODE;
    ledc_timer.timer_num = LEDC_HS_TIMER;
    ledc_timer_config(&ledc_timer);
#endif
    /*
     * Prepare individual configuration
     * for each channel of LED Controller
     * by selecting:
     * - controller's channel number
     * - output duty cycle, set initially to 0
     * - GPIO number where LED is connected to
     * - speed mode, either high or low
     * - timer servicing selected channel
     *   Note: if different channels use one timer,
     *         then frequency and bit_num of these channels
     *         will be the same
     */
    ledc_channel_config_t ledc_channel[LEDC_TEST_CH_NUM] = {
#ifdef CONFIG_IDF_TARGET_ESP32
        {
            .channel    = LEDC_HS_CH0_CHANNEL,
            .duty       = 0,
            .gpio_num   = LEDC_HS_CH0_GPIO,
            .speed_mode = LEDC_HS_MODE,
            .hpoint     = 0,
            .timer_sel  = LEDC_HS_TIMER
        },
        {
            .channel    = LEDC_HS_CH1_CHANNEL,
            .duty       = 0,
            .gpio_num   = LEDC_HS_CH1_GPIO,
            .speed_mode = LEDC_HS_MODE,
            .hpoint     = 0,
            .timer_sel  = LEDC_HS_TIMER
        },
#elif defined CONFIG_IDF_TARGET_ESP32S2
        {
            .channel    = LEDC_LS_CH0_CHANNEL,
            .duty       = 0,
            .gpio_num   = LEDC_LS_CH0_GPIO,
            .speed_mode = LEDC_LS_MODE,
            .hpoint     = 0,
            .timer_sel  = LEDC_LS_TIMER
        },
        {
            .channel    = LEDC_LS_CH1_CHANNEL,
            .duty       = 0,
            .gpio_num   = LEDC_LS_CH1_GPIO,
            .speed_mode = LEDC_LS_MODE,
            .hpoint     = 0,
            .timer_sel  = LEDC_LS_TIMER
        },
#endif
        {
            .channel    = LEDC_LS_CH2_CHANNEL,
            .duty       = 0,
            .gpio_num   = LEDC_LS_CH2_GPIO,
            .speed_mode = LEDC_LS_MODE,
            .hpoint     = 0,
            .timer_sel  = LEDC_LS_TIMER
        },
        {
            .channel    = LEDC_LS_CH3_CHANNEL,
            .duty       = 0,
            .gpio_num   = LEDC_LS_CH3_GPIO,
            .speed_mode = LEDC_LS_MODE,
            .hpoint     = 0,
            .timer_sel  = LEDC_LS_TIMER
        },
    };

    // Set LED Controller with previously prepared configuration
    for (ch = 0; ch < LEDC_TEST_CH_NUM; ch++) {
        ledc_channel_config(&ledc_channel[ch]);
    }

    // Initialize fade service.
    ledc_fade_func_install(0);

    while (1) {
        printf("1. LEDC fade up to duty = %d\n", LEDC_TEST_DUTY);
        for (ch = 0; ch < LEDC_TEST_CH_NUM; ch++) {
            ledc_set_fade_with_time(ledc_channel[ch].speed_mode,
                    ledc_channel[ch].channel, LEDC_TEST_DUTY, LEDC_TEST_FADE_TIME);
            ledc_fade_start(ledc_channel[ch].speed_mode,
                    ledc_channel[ch].channel, LEDC_FADE_NO_WAIT);
        }
        vTaskDelay(LEDC_TEST_FADE_TIME / portTICK_PERIOD_MS);

        printf("2. LEDC fade down to duty = 0\n");
        for (ch = 0; ch < LEDC_TEST_CH_NUM; ch++) {
            ledc_set_fade_with_time(ledc_channel[ch].speed_mode,
                    ledc_channel[ch].channel, 0, LEDC_TEST_FADE_TIME);
            ledc_fade_start(ledc_channel[ch].speed_mode,
                    ledc_channel[ch].channel, LEDC_FADE_NO_WAIT);
        }
        vTaskDelay(LEDC_TEST_FADE_TIME / portTICK_PERIOD_MS);

        printf("3. LEDC set duty = %d without fade\n", LEDC_TEST_DUTY);
        for (ch = 0; ch < LEDC_TEST_CH_NUM; ch++) {
            ledc_set_duty(ledc_channel[ch].speed_mode, ledc_channel[ch].channel, LEDC_TEST_DUTY);
            ledc_update_duty(ledc_channel[ch].speed_mode, ledc_channel[ch].channel);
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);

        printf("4. LEDC set duty = 0 without fade\n");
        for (ch = 0; ch < LEDC_TEST_CH_NUM; ch++) {
            ledc_set_duty(ledc_channel[ch].speed_mode, ledc_channel[ch].channel, 0);
            ledc_update_duty(ledc_channel[ch].speed_mode, ledc_channel[ch].channel);
        }
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

{{< / highlight >}}