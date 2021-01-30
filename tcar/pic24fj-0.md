// Configuration Registers.
#pragma config WPFP = WPFP511, WPDIS = WPDIS, WPCFG = WPCFGDIS
#pragma config WPEND = WPENDMEM, POSCMOD = EC, IOL1WAY = ON, OSCIOFNC = OFF
#pragma config FCKSM = CSDCMD, FNOSC = PRI, IESO = OFF, WDTPS = PS32768
#pragma config FWPSA = PR128, WINDIS = OFF, FWDTEN = OFF, ICS = PGx2
#pragma config GWRP = OFF, GCP = OFF, JTAGEN = OFF

// Definitions MCU Frequency.
#define FOSC    (24576000UL)
#define FCY     (FOSC/2)

#include <xc.h>
#include <libpic30.h>
// PIC24FJ64GA106 - Compile with XC16(v1.61).
// PIC24FJ64GA106 - @24.576MHz External Oscillator.
// v0.1 - 01/2021.

// Motors test.
// Forward  3seconds.
// Brake    1seconds.
// Backward 3seconds.
// Brake    1seconds.

// Definitions.
// Motors.
#define MOTOR_FRONT_LEFT    PORTDbits.RD5
#define MOTOR_FRONT_RIGHT   PORTDbits.RD4
#define MOTOR_REAR_LEFT     PORTBbits.RB9
#define MOTOR_REAR_RIGHT    PORTBbits.RB14
#define MOTOR_STOP          do{LATDbits.LATD7 = 0b0;} while(0)
#define MOTOR_nSTOP         do{LATDbits.LATD7 = 0b1;} while(0)
#define MOTOR_BRAKE         do{LATBbits.LATB13 = 0b1;} while(0)
#define MOTOR_nBRAKE        do{LATBbits.LATB13 = 0b0;} while(0)

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
    TRISB = 0b0000000000000001;
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

    // Forward.
    MOTOR_nSTOP;
    MOTOR_nBRAKE;
    __delay_us(1);
    MOTOR_FRONT_LEFT = 0b1;
    __delay_us(1);
    MOTOR_FRONT_RIGHT = 0b1;
    __delay_us(1);
    MOTOR_REAR_LEFT = 0b1;
    __delay_us(1);
    MOTOR_REAR_RIGHT = 0b1;
    __delay_ms(3000);

    // Brake.
    MOTOR_BRAKE;
    __delay_ms(1000);

    // Backward.
    MOTOR_nSTOP;
    MOTOR_nBRAKE;
    __delay_us(1);
    MOTOR_FRONT_LEFT = 0b0;
    __delay_us(1);
    MOTOR_FRONT_RIGHT = 0b0;
    __delay_us(1);
    MOTOR_REAR_LEFT = 0b0;
    __delay_us(1);
    MOTOR_REAR_RIGHT = 0b0;
    __delay_ms(3000);

    // Brake.
    MOTOR_BRAKE;
    __delay_ms(1000);

    while(1){};
    return(0);
}