# ESP32 Tiny Total SMS Public v0.0.0.2
<br>
<h1>Release v0.0.0.2</h1>
The features are:
<ul>
 <li>TTGO VGA32 v1.x (ESP32S2 without PSRAM).</li>
 <li>PS/2 keyboard.</li>
 <li>Real internal DAC sound.</li>
 <li>VGA DAC R2R RGB222.</li>
 <li>Project compatible with Arduino IDE, PlatformIO, ArduinoDroid, and Web Editor.</li>
 <li>60 real frames per second.</li>
 <li>Based on Total SMS x86 SDL (ITotalJustice).</li>
 <li>ROMs up to 1 MB (1048576 bytes).</li>
</ul>


<br><br>
<h1>Preparation</h1>
You must use the flash download tool 3.9.2.x:<br><br>
We must select the ESP32 type:
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/flash00.gif'></center>
Next, we will select the files as shown in the attached screenshot, with the same offset values:
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/flash01.gif'></center>
And we will press start. If everything has been done correctly, we will only need to restart the ESP32. 
<br><br>
It is advisable to erase the ESP32 using the ERASE option to ensure that all partitions are clean.<br>
<br><br>


<h1>ROM extra</h1>
By default, we have a demo ROM (Teddy Boy USA) that can be launched from the OSD.<br>
To add an extra SMS ROM, we must select the file in SMS format, as well as offset 0x110000 in the flash.<br>
We do not need to re-record the rest of the files, such as the emulator, partitions, bootloader, etc., only the SMS ROM in position 0x110000.
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/extrom.gif'></center>
This offset is compatible with Arduino IDE and PlaformIO SPIFFS partitions, so no custom partition scheme has been used.
Subsequently, from the OSD, we can launch the saved ROM from the Launch ROM option (Flash 0x110000).<br>
For now, we can only save one ROM in the FLASH, and the 0x110000 area is not deleted, it is only overwritten up to the size of the file, unless we delete the ESP32. Therefore, since the emulator tries to find a valid SEGA header starting at position 0x110000, if we first write a large ROM with a valid header (128 KB), and then record another smaller one (8 KB) without a valid header, when searching it finds the largest valid one, since
from 8 KB onwards the 128 KB information has not been deleted, only the first 8 KB have been overwritten. With valid header ROMs, we will never have problems.
To find a valid SEGA header, always search for the following positions:<br><br>

| Hex    | Decimal |
| ------ | ------- |
| 0x7FF0 | 32752   |
| 0x3FF0 | 16368   |
| 0x1FF0 | 8176    |

<br>

Hence the potential problem of overlap if deletion is not performed.<br>
From the OSD, you can erase between 8 KB and 1 MB of Flash memory from position 0x110000.


<br><br>
<h1>Video modes</h1>
There are 8 video modes selectable at start-up, for 500 milliseconds, or on-the-fly, from the OSD under the <b>Video Mode</b> option:<br><br>

| Tecla | Video mode                         |
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
The OSD allows you to leave it open so that if a video mode is not supported by the monitor, you can change it without having to look at the screen.<br>
A full frame rate of over 60 real fps is achieved, as both cores of the ESP32 are being used, one for the CPU and the other for the video, with 
VDP sampling synchronisation. Currently, a full VDP emulation is not being performed, so some visual defects will be visible.

<br>
At start-up, the video mode is selected by pressing the 0 to 7 key, depending on the video mode.<br>
By default, it starts in 360x200 mode, but all others are permitted for greater compatibility, notably those named <b>apll</b> which resolve
the Espressif PLL lock, for which no official solution has been provided.

<br><br>
<h1>Sound</h1>
The ESP32's internal GPIO25 DAC is used, meaning that neither PWM nor PDM are used, nor, of course, any libraries.<br>
No ring buffer or auxiliary buffer is used, i.e. it is (0 RAM usage). It also has 0 lag, meaning that mixing is performed in real time and
immediately, specifically at 44100 Hz.<br>
All problems with Espressif's internal DAC are solved, since I do not use I2Sound.
In addition, the full range of the DAC is used, so the volume is very high without causing distortion.<br>
I have not used the Total SMS sound implementation, as it was designed for a ring buffer, and I am using real-time oscillators.<br>
A very good emulation is performed in PSG mode of the three square wave channels of the SN76489, as well as the noise channel in white noise mode and periodic noise mode.<br>
Since I do the mixing in real time, I have taken the trouble to make changes to the wave generation, compared to other emulators, specifically in 
the generation of the negative part of the wave, so that I can give more power to the FX sounds.<br>

<br><br>
<h1>Keyboard</h1>
For now, only one player is allowed, and the keys are:<br><br>

| Tecla     | Acción      |
| --------- | ------------|
|  F1       | OSD display |
|  ESC      | Exit OSD    |
| Arriba    | Select      |
| Abajo     | Select      |
| Enter     | Accept      |
|           |             |
|  A        | Button A    |
|  S        | Button B    |
| Arriba    | Up          |
| Abajo     | Down        |
| Izquierda | Left        |
| Derecha   | Right       |
<br>



<br><br>
<h1>UART</h1>
It is possible to burn a ROM from UART using any terminal application, such as putty, Realterm, VSCode terminal, or any similar application for 
Android, Linux, etc...<br>
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/tooltomeko.gif'></center>
You just need to convert the binary file to Hexadecimal ASCII, removing the 0x from each value and leaving a CR LN code for each line.<br><br>

<a href='https://tomeko.net/online_tools/file_to_hex.php?lang=en'>https://tomeko.net/online_tools/file_to_hex.php?lang=en</a>

The speed must be selected from the OSD.<br>

A valid file would have a style with 16 bytes per line (32 characters):<br><br>

<pre>
F3ED5631F0DF1865F5E7F1D3BEC9FFFF
E7AF0EBEEDA3F5F1ED7920F8C911E081
...
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
</pre>

<br>
<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/realterm00.gif'></center>
If you are using Windows Realterm, you need to set a delay for each line to be transmitted from the file, for example 1 ms, in the combobox (Line delay in ms). The same applies to other platforms.<br>

<center><img src='https://raw.githubusercontent.com/rpsubc8/ESP32TinyTotalSMSPublic/main/preview/realterm01.gif'></center>
