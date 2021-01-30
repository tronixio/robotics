# DRAFT - TCAR.

## 0.Contents.

- [1.](#)

## 1.

```c
// Configuration Registers.
#pragma config WPFP = WPFP511, WPDIS = WPDIS, WPCFG = WPCFGDIS
#pragma config WPEND = WPENDMEM, POSCMOD = EC, IOL1WAY = ON, OSCIOFNC = OFF
#pragma config FCKSM = CSDCMD, FNOSC = PRI, IESO = OFF, WDTPS = PS32768
#pragma config FWPSA = PR128, WINDIS = OFF, FWDTEN = OFF, ICS = PGx2
#pragma config GWRP = OFF, GCP = OFF, JTAGEN = OFF

// Definitions Frequency.
#define FOSC    (24576000UL)
#define FCY     (FOSC/2)

#include <xc.h>
#include <libpic30.h>
// PIC24FJ64GA106 - Compile with XC16(v1.61).
// PIC24FJ64GA106 - @24.576MHz External Oscillator.
// v0.1 - 01/2021.

//

// nSTOP Enable L298P
// STOP  disable L298P

// Definitions.
// LED.
#define LED_BATTERY_ON                  do{LATCbits.LATC13 = 0b1;} while(0)
#define LED_BATTERY_OFF                 do{LATCbits.LATC13 = 0b0;} while(0)
//
#define SWITCH_S1                       PORTBbits.RB8
#define SWITCH_S2                       PORTBbits.RB11
#define SWITCH_S3                       PORTFbits.RF4
#define SWITCH_S4                       PORTBbits.RB12
// Motors.
#define MOTOR_PWM_FREQUENCY				((FCY/15000)-1)
#define MOTOR_PWM_DUTY_CYCLE_STOP       410
#define MOTOR_PWM_FRONT_LEFT			OC1R
#define MOTOR_PWM_FRONT_RIGHT			OC2R
#define MOTOR_PWM_REAR_LEFT				OC3R
#define MOTOR_PWM_REAR_RIGHT			OC4R
#define MOTOR_STOP                      do{LATDbits.LATD7 = 0b0;} while(0)
#define MOTOR_nSTOP                     do{LATDbits.LATD7 = 0b1;} while(0)
#define MOTOR_BRAKE                     do{LATBbits.LATB14 = 0b1;} while(0)
#define MOTOR_nBRAKE                    do{LATBbits.LATB14 = 0b0;} while(0)
// IR Encoders.
#define ENCODER_M12_ON                  do{LATDbits.LATD6 = 0b1;} while(0)
#define ENCODER_M12_OFF                 do{LATDbits.LATD6 = 0b0;} while(0)
#define ENCODER_M34_ON                  do{LATBbits.LATB10 = 0b1;} while(0)
#define ENCODER_M34_OFF                 do{LATBbits.LATB10 = 0b0;} while(0)

// Main.
int main(void)
{
    // MCU Initialization.
    // Oscillator Settings.
    CLKDIVbits.DOZE = 0b00;
    CLKDIVbits.DOZEN = 0b0;
    // Ports Initialization.
    // Analog Inputs Settings.
    AD1PCFGL = 0b1111111111111110;
    AD1PCFGH = 0b0000000000000011;
    // Port B Settings.
    TRISB = 0b0001100100000001;
    PORTB = 0b0000000000000000;
    LATB = 0b0000000000000000;
    ODCB = 0b0000000000000000;
    // Port C Settings.
    TRISC = 0b0000000000000000;
    PORTC = 0b0000000000000000;
    LATC = 0b0000000000000000;
    ODCC = 0b0000000000000000;
    // Port D Settings.
    TRISD = 0b0000000000000000;
    PORTD = 0b0000000000000000;
    LATD = 0b0000000000000000;
    ODCD = 0b0000000000000000;
    // Port E Settings.
    TRISE = 0b0000000000000000;
    PORTE = 0b0000000000000000;
    LATE = 0b0000000000000000;
    ODCE = 0b0000000000000000;
    // Port F Settings.
    TRISF = 0b0000000000010000;
    PORTF = 0b0000000000000000;
    LATF = 0b0000000000000000;
    ODCF = 0b0000000000000000;
    // Port G Settings.
    TRISG = 0b0000000000000000;
    PORTG = 0b0000000000000000;
    LATG = 0b0000000000000000;
    ODCG = 0b0000000000000000;
	// PPS Initialization.
	__builtin_write_OSCCONL(OSCCON & 0xBF);
	// PPS Inputs settings.
	// PPS Outputs settings.
	RPOR10bits.RP20R = 0x12;	// RD5  - RP20 - OC1.
	RPOR12bits.RP25R = 0x13;	// RD4  - RP25 - OC2.
    RPOR4bits.RP9R = 0x14;      // RB9  - RP9  - OC3.
	RPOR7bits.RP14R = 0x15;     // RB14 - RP14 - OC4.
	__builtin_write_OSCCONL(OSCCON | 0x40);

	// OutputCompare1 Settings.
	OC1CON1bits.OCSIDL = 0b1;
	OC1CON1bits.OCTSEL = 0b111;
	OC1CON1bits.ENFLT0 = 0b0;
	OC1CON1bits.TRIGMODE = 0b0;
	OC1CON1bits.OCM = 0b110;
	OC1CON2bits.FLTMD = 0b0;
	OC1CON2bits.FLTOUT = 0b0;
	OC1CON2bits.FLTTRIEN = 0b0;
	OC1CON2bits.OCINV = 0b0;
	OC1CON2bits.OC32 = 0b0;
	OC1CON2bits.OCTRIG = 0b0;
	OC1CON2bits.OCTRIS = 0b0;
	OC1CON2bits.SYNCSEL = 0b11111;
	OC1RS = MOTOR_PWM_FREQUENCY;
	OC1R = MOTOR_PWM_DUTY_CYCLE_STOP;
    
	// OutputCompare2 Settings.
	OC2CON1bits.OCSIDL = 0b1;
	OC2CON1bits.OCTSEL = 0b111;
	OC2CON1bits.ENFLT0 = 0b0;
	OC2CON1bits.TRIGMODE = 0b0;
	OC2CON1bits.OCM = 0b110;
	OC2CON2bits.FLTMD = 0b0;
	OC2CON2bits.FLTOUT = 0b0;
	OC2CON2bits.FLTTRIEN = 0b0;
	OC2CON2bits.OCINV = 0b0;
	OC2CON2bits.OC32 = 0b0;
	OC2CON2bits.OCTRIG = 0b0;
	OC2CON2bits.OCTRIS = 0b0;
	OC2CON2bits.SYNCSEL = 0b11111;
	OC2RS = MOTOR_PWM_FREQUENCY;
	OC2R = MOTOR_PWM_DUTY_CYCLE_STOP;

    // OutputCompare3 Settings.
	OC3CON1bits.OCSIDL = 0b1;
	OC3CON1bits.OCTSEL = 0b111;
	OC3CON1bits.ENFLT0 = 0b0;
	OC3CON1bits.TRIGMODE = 0b0;
	OC3CON1bits.OCM = 0b110;
	OC3CON2bits.FLTMD = 0b0;
	OC3CON2bits.FLTOUT = 0b0;
	OC3CON2bits.FLTTRIEN = 0b0;
	OC3CON2bits.OCINV = 0b0;
	OC3CON2bits.OC32 = 0b0;
	OC3CON2bits.OCTRIG = 0b0;
	OC3CON2bits.OCTRIS = 0b0;
	OC3CON2bits.SYNCSEL = 0b11111;
	OC3RS = MOTOR_PWM_FREQUENCY;
	OC3R = MOTOR_PWM_DUTY_CYCLE_STOP;

	// OutputCompare4 Settings.
	OC4CON1bits.OCSIDL = 0b1;
	OC4CON1bits.OCTSEL = 0b111;
	OC4CON1bits.ENFLT0 = 0b0;
	OC4CON1bits.TRIGMODE = 0b0;
	OC4CON1bits.OCM = 0b110;
	OC4CON2bits.FLTMD = 0b0;
	OC4CON2bits.FLTOUT = 0b0;
	OC4CON2bits.FLTTRIEN = 0b0;
	OC4CON2bits.OCINV = 0b0;
	OC4CON2bits.OC32 = 0b0;
	OC4CON2bits.OCTRIG = 0b0;
	OC4CON2bits.OCTRIS = 0b0;
	OC4CON2bits.SYNCSEL = 0b11111;
	OC4RS = MOTOR_PWM_FREQUENCY;
	OC4R = MOTOR_PWM_DUTY_CYCLE_STOP;

    // IR Wheels Encoder Enable.
    ENCODER_M12_ON;
    ENCODER_M34_ON;

    MOTOR_STOP;
    while(1){
        if(!SWITCH_S1){
        	OC1R = 820;
        }else{
        	OC1R = MOTOR_PWM_DUTY_CYCLE_STOP;
        }
        if(!SWITCH_S2){
        	OC3R = 820;
        }else{
        	OC3R = MOTOR_PWM_DUTY_CYCLE_STOP;
        }
        if(!SWITCH_S3){
        	OC2R = 820;
        }else{
        	OC2R = MOTOR_PWM_DUTY_CYCLE_STOP;
        }
        if(!SWITCH_S4){
        	OC4R = 820;
        }else{
        	OC4R = MOTOR_PWM_DUTY_CYCLE_STOP;
        }
    }
    return(0);
}
```

---
DISCLAIMER: THIS CODE IS PROVIDED WITHOUT ANY WARRANTY OR GUARANTEES.
USERS MAY USE THIS CODE FOR DEVELOPMENT AND EXAMPLE PURPOSES ONLY.
AUTHORS ARE NOT RESPONSIBLE FOR ANY ERRORS, OMISSIONS, OR DAMAGES THAT COULD
RESULT FROM USING THIS FIRMWARE IN WHOLE OR IN PART.