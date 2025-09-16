# ESP32 Tiny Total SMS Public v0.0.0.1
<br>
<h1>Release v0.0.0.1</h1>
The features are:
<ul>
 <li>TTGO VGA32 v1.x (ESP32S2 without PSRAM).</li>
 <li>PS/2 keyboard.</li>
 <li>Real internal DAC sound.</li>
 <li>VGA DAC R2R RGB222.</li>
 <li>Project compatible with Arduino IDE, PlatformIO, ArduinoDroid, and Web Editor.</li>
 <li>60 real frames per second.</li>
 <li>Based on Total SMS x86 SDL (ITotalJustice).</li>
 <li>256 KB ROMs (262155 bytes).</li>
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
For now, we can only save one ROM in the FLASH, and the 0x110000 area is not deleted, it is only overwritten up to the size of the file, unless we delete
the ESP32. Therefore, given that the emulator attempts to find a valid SEGA header starting at position 0x110000, if we first record a large ROM with 
a valid header (128 KB), and then we record another smaller one (32 KB) without a valid header, when searching it finds the larger valid one, since
from 32 KB onwards the 128 KB information has not been deleted, only the 32 KB has been overwritten. With valid header ROMs, we will never have problems.<br>
For now, although the tool allows recording, only 256 KB (262155 bytes) ROMS will read properly in the emulator.


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
