#include "stm32l0xx.h"                  // Device header
#include "string.h"


void Timer2_Enable(void);
void RCC_Enable(void);
void GPIO_Enable(void);

typedef struct
{
	uint8_t RxBuff[100];
	uint8_t TxBuff[100];
	uint8_t RxCnt;
	uint8_t TxCnt;
	uint8_t TxLen;
}UART__USB;

UART__USB uart;
uint8_t bit=1;
uint8_t index=0;


int main(void)
{
	uart.TxLen = sizeof("I am Using STM32 Nucleo board L053R8 with virtual com port on PA2\r\n");
	memcpy((uint8_t*)uart.TxBuff , (uint8_t*)"I am Using STM32 Nucleo board L053R8 with virtual com port on PA2\r\n" ,uart.TxLen );
  RCC_Enable();
	GPIO_Enable();
	
	Timer2_Enable();

	while(1)
	{
		while(++index<uart.TxLen)
		{
			bit=1;
			GPIOA->ODR &= ~(GPIO_ODR_OD2);
			TIM2->CNT=0x00;
			while(TIM2->CNT<=TIM2->ARR-2);
			for(uint8_t i=0;i<8;i++)
			{
				if(bit & uart.TxBuff[uart.TxCnt])
				{
					GPIOA->ODR |= (GPIO_ODR_OD2);
				}
				else
				{
					GPIOA->ODR &= ~(GPIO_ODR_OD2);
				}
				TIM2->CNT=0x00;
				while(TIM2->CNT<=TIM2->ARR-2);
				bit <<= 1;
			}
			uart.TxCnt++;
			GPIOA->ODR |= (GPIO_ODR_OD2);
			TIM2->CNT=0x00;
			while(TIM2->CNT<=TIM2->ARR-2);
		}
		uart.TxCnt=0;
		index=0;
	}
}

void RCC_Enable(void)
{
	RCC->CR |= RCC_CR_HSION;
	while(!(RCC->CR & RCC_CR_HSIRDY));
	
	RCC->CFGR &= ~(RCC_CFGR_SW_1);
	RCC->CFGR |= (RCC_CFGR_SW_0);   // Use HSI16 as system clock
}

void GPIO_Enable(void)
{
	RCC->IOPENR |= (RCC_IOPENR_GPIOAEN); //Enable Port C clock 
	
	GPIOA->MODER &= ~(GPIO_MODER_MODE2_1);
	GPIOA->MODER |= (GPIO_MODER_MODE2_0);  //Make output to PC4
	
	GPIOA->OTYPER &= ~(GPIO_OTYPER_OT_2);
	
	GPIOA->OSPEEDR |= (GPIO_OSPEEDER_OSPEED2);
	
	GPIOA->PUPDR &= ~(GPIO_PUPDR_PUPD2_1);
	GPIOA->PUPDR |= (GPIO_PUPDR_PUPD2_0);
	
	GPIOA->ODR |= (GPIO_ODR_OD2);   //Output High;
}

void Timer2_Enable(void)
{
	RCC->APB1ENR |= RCC_APB1ENR_TIM2EN;  
	//our system clock freqency is 16MHz
	
	TIM2->CR1 &= ~(TIM_CR1_UDIS);
	TIM2->CR1 |= (TIM_CR1_URS);
	TIM2->DIER |= (TIM_DIER_UIE);
	
	
	//16000000/(16-1) = 1000000;
	TIM2->PSC = 16-1;  //Now system frequency became 1000000 Hz  //1 tick is 1 micro second
	TIM2->ARR = 105; 
	TIM2->CR1 |= TIM_CR1_CEN;
}

