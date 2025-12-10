## Código en Arduino

```codigo

#include "BluetoothSerial.h"  

// Esta librería permite comunicar el ESP32 como si fuera un puerto inalámbrico.
 
// -----------------------------------------------------------------------------

// ----------------------------- VARIABLES GLOBALES -----------------------------

// -----------------------------------------------------------------------------
 
float Kp = 0.2;  

// Constante de Kp
 
String device_name = "ESP32_ANA";  

// Nombre que tendrá el dispositivo cuando se vea desde el Bluetooth de la copmutadora
 
// --------------------------- VALIDACIONES DEL ESP32 ---------------------------

// Estas validaciones revisan que el Bluetooth esté habilitado en la configuración

// del ESP32 antes de compilar. Si no lo está, el programa no compilará y marcará error.
 
#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)

#error Bluetooth is not enabled! Please run `make menuconfig` to and enable it

#endif
 
// Verifica si el perfil SPP (Serial Port Profile) está disponible

#if !defined(CONFIG_BT_SPP_ENABLED)

#error Serial Port Profile for Bluetooth is not available or not enabled. It is only available for the ESP32 chip.

#endif
 
// -----------------------------------------------------------------------------

// ----------------------------- OBJETOS Y STRINGS ------------------------------

// -----------------------------------------------------------------------------
 
BluetoothSerial SerialBT;

// Objeto principal que permite enviar/recibir datos por Bluetooth como si fuera Serial
 
String msj = "";       // Guardará la línea completa recibida desde Serial

String errorx = "";    // Parte del mensaje que contiene el valor en X

String errory = "";    // Parte del mensaje que contiene el valor en Y
 
// -----------------------------------------------------------------------------

// ---------------------------------- SETUP ------------------------------------

// -----------------------------------------------------------------------------
 
void setup() {

  Serial.begin(115200);  

  // Inicializa la comunicación serial por USB a 115200 baudios

  // Esto permite ver datos en el Serial Monitor
 
  SerialBT.begin(device_name);  

  // Inicia el Bluetooth con el nombre especificado en device_name

  // Ahora el ESP32 aparecerá como "ESP32_ANA" en la lista de dispositivos BT
 
  // SerialBT.deleteAllBondedDevices();  

  // (Opcional) Si se descomenta, borra dispositivos emparejados anteriormente

  // Útil si el teléfono no conecta porque cree que el dispositivo "ya existe".
 
  // Mensaje inicial que confirma que el Bluetooth está activo

  Serial.printf(

    "The device with name \"%s\" is started.\n"

    "Now you can pair it with Bluetooth!\n",

    device_name.c_str()

  );

}
 
// -----------------------------------------------------------------------------

// ----------------------------------- LOOP ------------------------------------

// -----------------------------------------------------------------------------
 
void loop() {
 
  // ---------------------------------------------------------------

  // Si llegan datos desde el puerto Serial USB se procesarán aquí.

  // ---------------------------------------------------------------
 
  if (Serial.available()) {

    // Lee hasta encontrar un salto de línea

    // Ejemplo: "120,-35\n"

    msj = Serial.readStrin

 ```