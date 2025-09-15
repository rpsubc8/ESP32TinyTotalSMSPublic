# ESP32 Tiny Total SMS Public v0.0.0.1
<br><br>
<h1>Release v0.0.0.1</h1>
Las características son:
<ul>
 <li>TTGO VGA32 v1.x (ESP32S2 sin PSRAM).</li>
 <li>Teclado PS/2.</li>
 <li>Sonido DAC real interno.</li>
 <li>VGA DAC222.</li>
 <li>Proyecto compatible con Arduino IDE, PlatformIO, ArduinoDroid y Web Editor.</li>
 <li>60 fps reales</li>
</ul>


<br><br>
<h1>Preparación</h1>
Se debe usar la herramienta flash download tool 3.9.2.x:<br><br>
Debemos de elegir el tipo ESP32:
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/flash00.gif'></center>
Posteriormente, seleccionaremos los archivos tal y como la captura adjunta, con los mismos valores de offset:
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/flash01.gif'></center>
Y le daremos a start. Si todo ha sido correcto, sólo tendremos que reiniciar el ESP32. 
<br><br>
Es recomendable hacer un borrado del ESP32 con la opción ERASE, para tener todas las particiones limpias.<br>
<br><br>


<h1>ROM extra</h1>
Por defecto, tenemos una ROM de demostración (Teddy Boy USA) que puede ser lanzada desde el OSD.<br>
Para añadir una ROM extra SMS, debemos elegir el archivo en formato SMS, así como el offset 0x110000 en la flash.<br>
No necesitamos volver a grabar el resto de archivos, como el emulador, particiones, booloader, etc..., tan sólo la rom SMS en la posición 0x110000.
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/extrom.gif'></center>
Este offset es compatible con las particiones SPIFFS de Arduino IDE y PlaformIO, por lo que no se ha usado ningún esquema de partición custom.
Posteriormente, desde el OSD podremos lanzar la ROM guardada, desde la opción Lanzar ROM (Flash 0x110000).<br>
Por ahora, sólo podemos guardar una ROM en la FLASH, y la zona 0x110000 no se borra, tan sólo se sobreescribe hasta el tamaño del fichero, salvo que hagamos un borrado
del ESP32. Por tanto, dado que el emulador intenta buscar una cabecera válida de SEGA a partir de la posición 0x110000, si primero grabamos una ROM grande con 
una cabecera válida (128 KB), y posteriormente grabamos otra de menor tamaño (32 KB) sin cabecera válida, al buscar encuentra la válida más grande, dado que a
partir de 32 KB no se ha borrado la información de los 128 KB, tan sólo se ha sobreescrito los 32 KB. Con roms de cabecera válida, nunca tendremos problemas.


<br><br>
<h1>Modos de video</h1>
Existen 7 modos de video seleccionables en el arranque, durante 500 milisegundos, o en caliente, desde el OSD en la opción <b>Modo de video</b>:<br><br>

| Tecla | Modo de video                      |
| ----- | -----------------------------------|
|   0   | 360x200x70hz bitluni(6bpp)         |
|   1   | 360x200x70hz bitluni apll(6bpp)    |
|   2   | 320x200x70hz bitluni(6bpp)         |
|   3   | 320x200x70hz fabgl(6bpp)           |
|   4   | 320x200x70hz bitluni apll(6bpp)    |
|   5   | 320x240x60hz bitluni(6bpp)         |
|   6   | 320x240x60hz fabgl(6bpp)           |
|   7   | 320x240x60hz bitluni apll(6bpp)    |
<br>
En el OSD se deja la opción de no cerrar el mismo, para en caso de que un modo de video no sea soportado por el monitor, que podamos cambiarlo a ciegas.<br>
Se logra un fullframe de más de 60 fps reales, dado que se está utilizando los 2 cores del ESP32, uno para la CPU y otro para el video, con una 
sincronización de muestreo del VDP. Actualmente, no se está realizando una emulación completa del VDP, por lo que algún defecto visual, será visible.

<br>
En el arranque, el modo de video se realiza pulsando la tecla 0 al 7, según el modo de video.<br>
Por defecto se arranca en el modo 360x200, pero se permiten todos los demás, para mayor compatibilidad, destacando aquellos con nombre <b>apll</b> que solucionan
el bloqueo del PLL de Espressif, que oficialmente no han dado solución.

<br><br>
<h1>Sonido</h1>
Se utiliza el DAC interno GPIO25 del ESP32, es decir, que no se usa ni PWM, ni PDM, ni por supuesto, ninguna librería.<br>
No se utiliza ni ringbuffer, ni ningún buffer auxiliar, es decir, es (0 RAM usage). Además es 0 lag, es decir, que el mezclador se realiza en tiempo real y
de manera inmediata,en concreto en 44100 Hz.<br>
Se logra solucionar todos los problemas que tiene Espressif con el DAC interno, dado que no hago uso del I2Sound.<br>
Además se usa la amplitud total del DAC, por lo que el volumen es muy alto, sin causar la distorsión.<br>
No he usado la implementación de sonido del Total SMS, dado que estaba pensada para un ringbuffer, y yo estoy utilizando osciladores en tiempo real.<br>
Se realiza una emulación en modo PSG, de los 3 canales cuadrados del SN76489 muy buena, así como del canal del ruido en modo ruido blanco y en modo ruido periódico.<br>
Dado que la mezcla, la realizo en tiempo real, me he tomado la molestia de hacer cambios en la generación de la onda, respecto a otros emuladores, en concreto en 
la generación de la parte negativa de la misma, de manera, que logro dar más potencia a los sonidos FX.<br>

<br><br>
<h1>Teclado</h1>
Por ahora, sólo se deja un jugador, y las teclas son:<br><br>

| Tecla     | Acción      |
| --------- | ------------|
|  F1       | Muestra OSS |
|  ESC      | Salir OSD   |
| Arriba    | Seleccionar |
| Abajo     | Seleccionar |
| Enter     | Aceptar     |
|           |             |
|  A        | Botón A     |
|  S        | Botón B     |
| Arriba    | Arriba      |
| Abajo     | Abajo       |
| Izquierda | Izquierda   |
| Derecha   | Derecha     |
<br>

 "ENTER    - Pausa",

<br>
