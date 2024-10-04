# Advanced-easy-to-use-burgler-alarm
## **Overview**
This project introduces a Smart Burglar Alarm designed to secure homes, offices, and other spaces through precise object detection using ultrasonic sensing technology. The system is user-friendly, featuring an automatic threshold-setting mechanism to detect intrusions accurately. It adjusts itself to monitor objects based on environmental conditions, making it adaptable and reducing false alarms.

At the heart of the system is the VSD Squadron Mini, powered by a RISC-V CH32V003 microcontroller, which processes sensor data and triggers the alarm. Additionally, the PCB was custom-designed and etched manually, ensuring a compact and reliable implementation.

### **Objectives**
Build a smart burglar alarm that automatically sets detection thresholds.
Utilize the VSD Squadron Mini for efficient signal processing and system control.
Design and fabricate a custom PCB to house all components, ensuring an optimized layout.

## **System Architecture**
- **Detection Mechanism**:
- **Ultrasonic Sensor**: Responsible for detecting objects within a defined range, emitting sound waves and calculating distances based on the echo.
Automatic Threshold Setting: The system sets its own threshold based on real-time environmental feedback, ensuring optimal sensitivity without manual calibration.
- **Core Controller**:
- **VSD Squadron Mini (RISC-V CH32V003)**: This serves as the primary processing unit, handling the data from the ultrasonic sensor and controlling the alarm system. The use of the RISC-V architecture allows for low-power, high-efficiency processing.
- **Custom PCB Design**:
The custom-designed and manually etched PCB houses the ultrasonic sensor, VSD Squadron Mini, and supporting components. This PCB reduces the need for complex wiring and enhances reliability.
- **User Interaction**:
The system is designed to be plug-and-play, with minimal setup required. Once installed, it automatically adjusts to its surroundings and triggers alarms when an object crosses the set threshold.

## **Technical Specifications**
- **Sensor**: Ultrasonic sensor for object detection.
- **Microcontroller**: VSD Squadron Mini (RISC-V CH32V003) for signal processing and alarm control.
- **Custom PCB**: Designed and manually etched for compact integration of components.
- **Power Supply**: Supports standard 5V or 12V power inputs.
- **Alarm System**: Activates when an object is detected within the preset distance threshold.
  
## **circut diagram**
![image](https://github.com/user-attachments/assets/e90bb9e8-fca1-408f-937a-7c4114e61e9b)

code
```
#include "debug.h"

uint16_t distance;
uint16_t press;

void Input_Capture_Init(uint16_t arr, uint32_t psc)
{
    GPIO_InitTypeDef        GPIO_InitStructure = {0};
    TIM_ICInitTypeDef       TIM_ICInitStructure = {0};
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure = {0};
    NVIC_InitTypeDef        NVIC_InitStructure = {0};

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD | RCC_APB2Periph_GPIOC | RCC_APB2Periph_TIM1, ENABLE);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPD;
    GPIO_Init(GPIOD, &GPIO_InitStructure);
    GPIO_ResetBits(GPIOD, GPIO_Pin_2);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_3;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPD;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_3 | GPIO_Pin_4;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_7;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    TIM_TimeBaseInitStructure.TIM_Period = arr;
    TIM_TimeBaseInitStructure.TIM_Prescaler = psc;
    TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
    TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInitStructure.TIM_RepetitionCounter = 0x00;
    TIM_TimeBaseInit(TIM1, &TIM_TimeBaseInitStructure);

    TIM_ICInitStructure.TIM_Channel = TIM_Channel_1;
    TIM_ICInitStructure.TIM_ICPrescaler = TIM_ICPSC_DIV1;
    TIM_ICInitStructure.TIM_ICFilter = 0x00;
    TIM_ICInitStructure.TIM_ICPolarity = TIM_ICPolarity_Rising;
    TIM_ICInitStructure.TIM_ICSelection = TIM_ICSelection_DirectTI;

    TIM_PWMIConfig(TIM1, &TIM_ICInitStructure);

    NVIC_InitStructure.NVIC_IRQChannel = TIM1_CC_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0;
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init(&NVIC_InitStructure);

    TIM_ITConfig(TIM1, TIM_IT_CC1 | TIM_IT_CC2, ENABLE);

    TIM_SelectInputTrigger(TIM1, TIM_TS_TI1FP1);
    TIM_SelectSlaveMode(TIM1, TIM_SlaveMode_Reset);
    TIM_SelectMasterSlaveMode(TIM1, TIM_MasterSlaveMode_Enable);
    TIM_Cmd(TIM1, ENABLE);
}

uint16_t pressed(void){
    if(GPIO_ReadInputDataBit(GPIOC,GPIO_Pin_3)==1){
        Delay_Ms(500);
        GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
        Delay_Ms(100);
        GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
        Delay_Ms(1000);
        press=!press;
    }
    return press;
}

int main(void)
{
    SystemCoreClockUpdate();
    Delay_Init();
    USART_Printf_Init(115200);
    Input_Capture_Init(0xFFFF, 48 - 1);
    uint32_t count=0;
    uint32_t value=0;
    uint16_t avg=0;
    
    while (pressed())
    {     
        GPIO_WriteBit(GPIOD, GPIO_Pin_3, SET);
        Delay_Us(10); 
        GPIO_WriteBit(GPIOD, GPIO_Pin_3, RESET);
        if(count<=4000){
            count+=1;
            GPIO_WriteBit(GPIOD,GPIO_Pin_4,SET);
            value+=distance;
            Delay_Ms(1);
        }else if(count==4001){
            avg = value/count;
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
            Delay_Ms(100);
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
            Delay_Ms(100);
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
            Delay_Ms(100);
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
            Delay_Ms(100);
            count+=1;
        }else if(count>4001 && count<4050){
            count+=1;
            Delay_Ms(1);
        }else{
            GPIO_WriteBit(GPIOD,GPIO_Pin_4,RESET);
            if(distance<avg-10 || distance>avg+10){
                count=0;
                while(pressed()){
                    GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
                    GPIO_WriteBit(GPIOD,GPIO_Pin_4,SET);
                    Delay_Ms(500);
                    GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
                    GPIO_WriteBit(GPIOD,GPIO_Pin_4,RESET);
                    Delay_Ms(500);
                }
            }
        }  
    }
}



void TIM1_CC_IRQHandler(void) __attribute__((interrupt("WCH-Interrupt-fast")));

void TIM1_CC_IRQHandler(void)
{
    if (TIM_GetITStatus(TIM1, TIM_IT_CC1) != RESET)
    {
        TIM_SetCounter(TIM1,0);
    }

    if (TIM_GetITStatus(TIM1, TIM_IT_CC2) != RESET)
    {
        uint32_t duration = TIM_GetCapture1(TIM1);
        distance = duration*0.034/2;
        printf("%d\n",distance);
        
    }

    TIM_ClearITPendingBit(TIM1, TIM_IT_CC1 | TIM_IT_CC2);
}
```


## **Features**
- **Automatic Threshold Setting**: The system dynamically adjusts its detection range based on environmental factors, ensuring high accuracy.
- **VSD Squadron Mini**: The RISC-V based microcontroller powers the system with efficient processing, ensuring responsive and reliable alarm triggering.
- **Custom PCB**: A manually etched PCB reduces wiring complexity and ensures a neat, reliable assembly.
- **Object Detection**: The ultrasonic sensor guarantees precise detection of even minor intrusions.

## **Applications**
- Home and office security
- Securing warehouses and storage spaces
- Industrial surveillance
- Portable security for temporary setups

## **Future Enhancements**
- Adding wireless communication for remote monitoring and alerts.
- Integrating multiple sensors (e.g., infrared or motion detectors) for enhanced detection.
- Developing a mobile app to allow remote control and notifications.
