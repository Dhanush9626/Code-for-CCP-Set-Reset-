# Code-for-CCP-Set-Reset-
#include "stm32f401xe.h"
#include "stdint.h"

#define GPIOAEN   (1U<<0)   
#define TIM5EN    (1U<<4)   

void COMPARE_INIT(void);
void SET(void);
void RESET(void);

int main(void)
{
    COMPARE_INIT();

    while(1)
    {
        SET();
        for(volatile int i=0; i<100; i++);

        RESET();
        for(volatile int i=0; i<1000; i++);
    }
}

void COMPARE_INIT(void)
{
    RCC->AHB1ENR |= GPIOAEN;          
    GPIOA->MODER |=  (1<<4);     
    GPIOA->AFR[0] |=  (1<<5);    

    RCC->APB1ENR |= TIM5EN;         
    TIM5->PSC = 16000 - 1;            
    TIM5->ARR = 2000;               
    TIM5->CNT = 0;

    TIM5->CCER |= (1<<4);             
    TIM5->CCR2 = 1000;                

    TIM5->CR1 |= (1<<0);              
}

void SET(void)
{
    TIM5->CCMR1 |=  (1<<4);         
    TIM5->CCR2 = 1000;                
    while(!(TIM5->SR & TIM_SR_UIF));  
    TIM5->SR &= ~TIM_SR_UIF;
}

void RESET(void)
{
    TIM5->CCMR1 |=  (1<<5);         
    TIM5->CCR2 = 1500;                
    while(!(TIM5->SR & TIM_SR_UIF));  
    TIM5->SR &= ~TIM_SR_UIF;
}
