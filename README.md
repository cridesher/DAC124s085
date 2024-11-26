# DAC124s085
//Configure of the DAC124s085 with tiva TM4c123 with energia

#include <SPI.h>
#include <math.h>

#define DACA 0x00  //DAC A
#define DACB 0x40  //DAC B
#define DACC 0x80  //DAC C
#define DACD 0xC0  //DAC D

#define DAC_CONF 0x10 //write to specified register and update outputs
#define DAC_CONF1 0x00 // write to specified register but do not update outputs
#define DAC_CONF2 0x20 // write to all registers and update outputs
#define DAC_CONF3 0x30 // power-down outputs


int ss=6; // 6 es para la cs de DAC 10 bits tarjeta roja
float t;
unsigned valor =0;




void setup() {
  // put your setup code here, to run once:
pinMode(ss, OUTPUT);
//digitalWrite(ss, LOW);
SPI.begin();
}

void enviar(unsigned int valor_dac)
{
  
  char MSB, PARTE1, PARTE2;

  digitalWrite(ss, LOW);
  //division de la cadena de bits en 2 considerando que el protocolo SPI solo puede mandar 8 bits, y requerimos mandar 16 bits totales
  MSB = (valor_dac >>8);// & 0x0F; //se recorren 8 bits a la derecha 
  PARTE1 = DACA + DAC_CONF + MSB; // se suma a la cadena 1ra parte la DAC a usar, la conf a usar, la primera cadena de bits
  PARTE2 = valor_dac << 0; // aqui no es necesario hacer un recorrido de bits a la izquierda ya que usamos el buffer completo sin bits don't care

  // envio de la cadena de bits fragmentada en 2 partes
  digitalWrite(ss,LOW);
  
  SPI.transfer(PARTE1);
 
  SPI.transfer(PARTE2);
 
  digitalWrite(ss,HIGH);
  // fin del envio de la cadena de bits fragmentada en 2 paartes
  
}

void loop() {
  // put your main code here, to run repeatedly: 
  for (int i; i<80;i++) // funcion seno para prueba de salida de DAC
  {
    t=t+0.078;
    valor =(unsigned int) 2048*sin(t)+2048; 
    enviar(valor);
    //delayMicroseconds(8.5);
    if (t > 2*3.14) t=0;
  }

