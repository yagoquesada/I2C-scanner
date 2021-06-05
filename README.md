# PRÁCTICA 5.1: ESCÁNER I2C

## CÓDIGO
```ruby
#include <Arduino.h>

#include <Wire.h>

#include "MAX30105.h"

#include <Adafruit_I2CDevice.h>

#include <Adafruit_GFX.h>

#include <Adafruit_SSD1306.h>
 
void setup()
{
  Wire.begin();
 
  Serial.begin(115200);
  while (!Serial);             // Leonardo: wait for serial monitor
  Serial.println("\nI2C Scanner");
}
 
 
void loop()
{
  byte error, address;
  int nDevices;
 
  Serial.println("Scanning...");
 
  nDevices = 0;
  for(address = 1; address < 127; address++ )
  {
    // The i2c_scanner
    // the Write.endTransmisstion to see if
    // a device did acknowledge to the address.
    Wire.beginTransmission(address);
    error = Wire.endTransmission();
 
    if (error == 0)
    {
      Serial.print("I2C device found at address 0x");
      if (address<16)
        Serial.print("0");
      Serial.print(address,HEX);
      Serial.println("  !");
 
      nDevices++;
    }
    else if (error==4)
    {
      Serial.print("Unknown error at address 0x");
      if (address<16)
        Serial.
        Serial.print("0");
      Serial.println(address,HEX);
    }    
  }
  if (nDevices == 0)
    Serial.println("No I2C devices found\n");
  else
    Serial.println("done\n");
 
  delay(5000);           // wait 5 seconds for next scan
}
```

## FUNCIONAMENTO

Para empezar añadimos las siguentes seis librerias:

```ruby
#include <Arduino.h>
#include <Wire.h>
#include "MAX30105.h"
#include <Adafruit_I2CDevice.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
```

A continuación empezamos el `void setup()`, este en la primera línea inicializaremos la biblioteca *Wire* con la función `Wire.begin()`, seguidamente establecemos la velocidad de datos, en este caso, 115200 bauds (bits por segundo) para la transmisión de datos en serie. Después con la línea de código `while(!Serial)` lo que estamos diciendo es que la placa espere a que el PC establezca una conexión serie activa (es decir, que el puerto serie se abra mediante un software). Al final del *setup* imprimimos un mensaje por pantalla.

```ruby
void setup()
{
  Wire.begin();
 
  Serial.begin(115200);
  while (!Serial);
  Serial.println("\nI2C Scanner");
}
```

A continuación empezamos el `void loop()`, la función de este será mirar en que dirección tenemos nuestro dispositivo. Empezaremos este *loop* por definir algunas variables e imprimir por pantalla "Scanning...", después con un *for* crearemos un bucle que se repetirá 127 veces, este nos permitirá detectar cualquier dispositivo I2C, la función `Wire.beginTransmission(address)` inicia una transmisión al dispositivo esclavo (*slave device*) I2C con la dirección dada. Y la función `Wire.endTransmission()` finaliza la transmisión iniciada por `Wire.beginTransmission(address)` y transmite los bytes puestos en cola por `write()`, estos bytes pueden tener los siguientes valores:

- 0: éxito
- 1: datos demasiado largos para caber en el búfer de transmisión
- 2: recibido NACK al transmitir la dirección
- 3: recibido NACK al transmitir datos
- 4: otro error 

Por lo tanto, tendremos un condicional que si el *error* es igual a 0 (`if (error == 0)`) se mostrará por el terminal la dirección (*address*), en cambio si el *error* es igual a 4 se mostrará por el terminal que hay un error en la conexión, y si no es ni igual a 0 ni igual a se mostrará que no se ha encontrado ningún dispositivo I2C.

```ruby
void loop()
{
  byte error, address;
  int nDevices;
 
  Serial.println("Scanning...");
 
  nDevices = 0;
  for(address = 1; address < 127; address++ )
  {
    Wire.beginTransmission(address);
    error = Wire.endTransmission();
 
    if (error == 0)
    {
      Serial.print("I2C device found at address 0x");
      if (address<16)
        Serial.print("0");
      Serial.print(address,HEX);
      Serial.println("  !");
 
      nDevices++;
    }
    else if (error==4)
    {
      Serial.print("Unknown error at address 0x");
      if (address<16)
        Serial.print("0");
      Serial.println(address,HEX);
    }    
  }
  if (nDevices == 0)
    Serial.println("No I2C devices found\n");
  else
    Serial.println("done\n");
 
  delay(5000);           
}
```

## SALIDA POR EL TERMINAL

Este programa si la conexión funciona correctamente (*error*=0) imprimirá lo siguiente por pantalla:

```
Scanning...
I2C device found at address 0x3C  ! 
done
```

Donde pone 3C aparecerá la dirección de tu dispositivo I2C.
