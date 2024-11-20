##Piezo Code Explanation and Walkthrough

1) In the .ioc file, go to Timers
2) Select TIM3, and select "PWM Generation CH1" for Channel 1
3) I set Prescaler to 71 and the ARR to 999
4) By default, ideally, the PA6 pin should be configured for the TIM3_CH1

After configuring the pins, save it to generate code

In the main.c src file:

Right above the main method:
```
void play_tone(uint16_t frequency, uint32_t duration_ms) {
    // Compute timer settings for the desired frequency
    uint32_t timer_clock = HAL_RCC_GetPCLK1Freq(); // Timer clock frequency
    uint32_t prescaler = (timer_clock / 1000000) - 1; // Set base clock to 1 MHz
    uint32_t period = 1000000 / frequency - 1; // Set period for desired frequency

    // Configure the timer
    TIM3->PSC = prescaler;  // Prescaler
    TIM3->ARR = period;     // Auto-reload register
    TIM3->CCR1 = period / 2; // 50% duty cycle

    // Start PWM
    HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);

    // Delay for the duration of the tone
    HAL_Delay(duration_ms);

    // Stop PWM
    HAL_TIM_PWM_Stop(&htim3, TIM_CHANNEL_1);
}
```

In the main method, inside the while loop:
```
play_tone(1000, 10000); // Play a 1 kHz tone for 10 second
HAL_Delay(1000);
```
