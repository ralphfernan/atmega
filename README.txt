
UBUNTU Microchip ATmega328 Programming via the Serial Peripheral Interface

Components:
1. A CP2102 (CP210x) USB-UART/TTL adapter.  This includes Tx, DTR, CTS, RTS, 5v and GND.
2. The microchip (ATMega 328)

FAQ:
No other resistors/capacitors/crystals/parts are needed for programming.  See circuit diagram.  For checking the blink program after programming the chip, you need an led and a current limiting resistor (few hundred ohms to couple of kilo-ohms).

Most recent Ubuntu versions should already include the CP210x USB driver.  

There is no need to program a bootloader.  In this case, the program (.hex) is uploaded through the Serial Peripheral Interface (SPI), specifically the SCK, SDO (MOSI) and SDI (MISO) pins.

If you want to upload your .hex program through some other interface besides SPI, then the specifications say “The On-chip ISPvFlash allows the program memory to be reprogrammed In-System through an SPI serial interface, by a conventional non-volatile memory programmer, or by an On-chip Boot program running on the AVR core. The Boot program can use any interface to download the application program in the Application Flash memory.”  
(This is where a bootloader is used.)

To check the fresh chip’s low fuse (for high fuse – hfuse, extended fuse – efuse):


~/avrprojects$ sudo avrdude -C avrdude.conf -c ralphusb -P cp210x -p atmega328 -U lfuse:r:-:h
[sudo] password for:
avrdude: AVR device initialized and ready to accept instructions
avrdude: device signature = 0x1e9514 (probably m328)

avrdude: processing -U lfuse:r:-:h
avrdude: reading lfuse memory ...
avrdude: writing output file <stdout>
0x62

avrdude done.  Thank you.

Note: the result 0x62 == b’0110 0010’ is the default.  See references below; Microchip specifications.  This specifies that the default internal 8MHz clock is divided by 8, i.e. a 1 MHz effective clock for a factory-fresh chip.

cd ~/avrprojects/sketches/blink-ATmega328p

$ sudo avrdude -C ../../avrdude.conf -c ralphusb -P cp210x -p atmega328 -i 16 -D -U flash:w:blink.hex

UBUNTU 20.x Setup

CP210x addition to avrdude.conf
(Using vi or linux text editor to avoid conversion issues)


Installing CP210x driver:
See Appendix A

Building avrdude (Microchip Atmel Microcontroller Programmer)

1. Get source e.g. avrdude-7.3.zip
2. Unzip to folder
3. Install required libs: https://github.com/avrdudes/avrdude/wiki/Building-AVRDUDE-for-Linux
For debian sudo apt-get install build-essential git cmake flex bison libelf-dev libusb-dev libhidapi-dev libftdi1-dev libreadline-dev libserialport-dev
3b.  (No errors; see “processing triggers”)
4. ./build.sh
4b.  See output in Appendix B

##################################################################################

APPENDIX A
Re: CP210x drivers
In Ubuntu 17.10, these kernel modules are already there. To see if they exist on your 14.04 system, in terminal do...
ls -al /lib/modules/"$(uname -r)"/kernel/drivers/usb/serial/usbserial.ko

ls -al /lib/modules/"$(uname -r)"/kernel/drivers/usb/serial/cp210x.ko
or
modinfo usbserial # get info on this kernel module

modinfo cp210x # get info on this kernel module
(Should not be required:) To manually load modules, so you can try access from Ubuntu, do:
sudo modprobe usbserial # load this kernel module

sudo modprobe cp210x # load this kernel module
Install/eject history:
$ dmesg | grep ttyUSB0
usb 2-2: cp210x converter now attached to ttyUSB0
cp210x ttyUSB0: cp210x converter now disconnected from ttyUSB0



APPENDIX B – BUILDING LATEST AVRDUDE
~/Downloads/avrdude-7.3$ ./build.sh 
-- The C compiler identification is GNU 9.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Found Git: /usr/bin/git (found version "2.25.1") 
-- Found FLEX: /usr/bin/flex (found version "2.6.4") 
-- Found BISON: /usr/bin/bison (found version "3.5.1")
-- Looking for libelf.h
-- Looking for libelf.h - found
-- Looking for libelf/libelf.h
-- Looking for libelf/libelf.h - not found
-- Looking for usb.h
-- Looking for usb.h - found
-- Looking for lusb0_usb.h
-- Looking for lusb0_usb.h - not found
-- Looking for libusb.h
-- Looking for libusb.h - not found
-- Looking for libusb-1.0/libusb.h
-- Looking for libusb-1.0/libusb.h - found
-- Looking for hidapi/hidapi.h
-- Looking for hidapi/hidapi.h - found
-- Looking for ftdi_tcioflush
-- Looking for ftdi_tcioflush - not found
-- Configuration summary:
-- ----------------------
-- DO HAVE    libelf
-- DO HAVE    libusb
-- DO HAVE    libusb_1_0
-- DO HAVE    libhidapi
-- DON'T HAVE libftdi
-- DO HAVE    libftdi1
-- DO HAVE    libreadline
-- DO HAVE    libserialport
-- DISABLED   doc
-- DISABLED   parport
-- DISABLED   linuxgpio
-- DISABLED   linuxspi
-- ----------------------
-- Configuring done
-- Generating done
-- Build files have been written to Downloads/avrdude-7.3/build_linux
[  1%] [BISON][Parser] Building parser with bison 3.5.1
[  2%] [FLEX][Parser] Building scanner with flex 2.6.4
Scanning dependencies of target libavrdude
[  3%] Building C object src/CMakeFiles/libavrdude.dir/arduino.c.o
[  5%] Building C object src/CMakeFiles/libavrdude.dir/avr.c.o
[  6%] Building C object src/CMakeFiles/libavrdude.dir/avr910.c.o
[  7%] Building C object src/CMakeFiles/libavrdude.dir/avrcache.c.o
[  9%] Building C object src/CMakeFiles/libavrdude.dir/avrftdi.c.o
[ 10%] Building C object src/CMakeFiles/libavrdude.dir/avrftdi_tpi.c.o
[ 11%] Building C object src/CMakeFiles/libavrdude.dir/avrintel.c.o
[ 13%] Building C object src/CMakeFiles/libavrdude.dir/avrpart.c.o
[ 14%] Building C object src/CMakeFiles/libavrdude.dir/bitbang.c.o
[ 15%] Building C object src/CMakeFiles/libavrdude.dir/buspirate.c.o
[ 17%] Building C object src/CMakeFiles/libavrdude.dir/butterfly.c.o
[ 18%] Building C object src/CMakeFiles/libavrdude.dir/ch341a.c.o
[ 19%] Building C object src/CMakeFiles/libavrdude.dir/config.c.o
[ 21%] Building C object src/CMakeFiles/libavrdude.dir/confwin.c.o
[ 22%] Building C object src/CMakeFiles/libavrdude.dir/crc16.c.o
[ 23%] Building C object src/CMakeFiles/libavrdude.dir/dfu.c.o
[ 25%] Building C object src/CMakeFiles/libavrdude.dir/dryrun.c.o
[ 26%] Building C object src/CMakeFiles/libavrdude.dir/fileio.c.o
[ 27%] Building C object src/CMakeFiles/libavrdude.dir/flip1.c.o
[ 28%] Building C object src/CMakeFiles/libavrdude.dir/flip2.c.o
[ 30%] Building C object src/CMakeFiles/libavrdude.dir/ft245r.c.o
[ 31%] Building C object src/CMakeFiles/libavrdude.dir/jtagmkI.c.o
[ 32%] Building C object src/CMakeFiles/libavrdude.dir/jtagmkII.c.o
[ 34%] Building C object src/CMakeFiles/libavrdude.dir/jtag3.c.o
[ 35%] Building C object src/CMakeFiles/libavrdude.dir/leds.c.o
[ 36%] Building C object src/CMakeFiles/libavrdude.dir/linuxgpio.c.o
[ 38%] Building C object src/CMakeFiles/libavrdude.dir/linuxspi.c.o
[ 39%] Building C object src/CMakeFiles/libavrdude.dir/lists.c.o
[ 40%] Building C object src/CMakeFiles/libavrdude.dir/micronucleus.c.o
[ 42%] Building C object src/CMakeFiles/libavrdude.dir/par.c.o
[ 43%] Building C object src/CMakeFiles/libavrdude.dir/pgm.c.o
[ 44%] Building C object src/CMakeFiles/libavrdude.dir/pgm_type.c.o
[ 46%] Building C object src/CMakeFiles/libavrdude.dir/pickit2.c.o
[ 47%] Building C object src/CMakeFiles/libavrdude.dir/pindefs.c.o
[ 48%] Building C object src/CMakeFiles/libavrdude.dir/ppi.c.o
[ 50%] Building C object src/CMakeFiles/libavrdude.dir/ppiwin.c.o
[ 51%] Building C object src/CMakeFiles/libavrdude.dir/serbb_posix.c.o
[ 52%] Building C object src/CMakeFiles/libavrdude.dir/serbb_win32.c.o
[ 53%] Building C object src/CMakeFiles/libavrdude.dir/ser_avrdoper.c.o
[ 55%] Building C object src/CMakeFiles/libavrdude.dir/ser_posix.c.o
[ 56%] Building C object src/CMakeFiles/libavrdude.dir/ser_win32.c.o
[ 57%] Building C object src/CMakeFiles/libavrdude.dir/serialadapter.c.o
[ 59%] Building C object src/CMakeFiles/libavrdude.dir/serialupdi.c.o
[ 60%] Building C object src/CMakeFiles/libavrdude.dir/stk500.c.o
[ 61%] Building C object src/CMakeFiles/libavrdude.dir/stk500v2.c.o
[ 63%] Building C object src/CMakeFiles/libavrdude.dir/stk500generic.c.o
[ 64%] Building C object src/CMakeFiles/libavrdude.dir/strutil.c.o
[ 65%] Building C object src/CMakeFiles/libavrdude.dir/teensy.c.o
[ 67%] Building C object src/CMakeFiles/libavrdude.dir/term.c.o
[ 68%] Building C object src/CMakeFiles/libavrdude.dir/updi_link.c.o
[ 69%] Building C object src/CMakeFiles/libavrdude.dir/updi_nvm.c.o
[ 71%] Building C object src/CMakeFiles/libavrdude.dir/updi_nvm_v0.c.o
[ 72%] Building C object src/CMakeFiles/libavrdude.dir/updi_nvm_v2.c.o
[ 73%] Building C object src/CMakeFiles/libavrdude.dir/updi_nvm_v3.c.o
[ 75%] Building C object src/CMakeFiles/libavrdude.dir/updi_nvm_v4.c.o
[ 76%] Building C object src/CMakeFiles/libavrdude.dir/updi_nvm_v5.c.o
[ 77%] Building C object src/CMakeFiles/libavrdude.dir/updi_readwrite.c.o
[ 78%] Building C object src/CMakeFiles/libavrdude.dir/updi_state.c.o
[ 80%] Building C object src/CMakeFiles/libavrdude.dir/urclock.c.o
[ 81%] Building C object src/CMakeFiles/libavrdude.dir/usbasp.c.o
[ 82%] Building C object src/CMakeFiles/libavrdude.dir/usb_hidapi.c.o
[ 84%] Building C object src/CMakeFiles/libavrdude.dir/usb_libusb.c.o
[ 85%] Building C object src/CMakeFiles/libavrdude.dir/usbtiny.c.o
[ 86%] Building C object src/CMakeFiles/libavrdude.dir/update.c.o
[ 88%] Building C object src/CMakeFiles/libavrdude.dir/wiring.c.o
[ 89%] Building C object src/CMakeFiles/libavrdude.dir/xbee.c.o
[ 90%] Building C object src/CMakeFiles/libavrdude.dir/__/lexer.c.o
[ 92%] Building C object src/CMakeFiles/libavrdude.dir/__/config_gram.c.o
[ 93%] Linking C static library libavrdude.a
[ 93%] Built target libavrdude
Scanning dependencies of target avrdude
[ 94%] Building C object src/CMakeFiles/avrdude.dir/main.c.o
[ 96%] Building C object src/CMakeFiles/avrdude.dir/developer_opts.c.o
[ 97%] Building C object src/CMakeFiles/avrdude.dir/whereami.c.o
[ 98%] Linking C executable avrdude
[ 98%] Built target avrdude
Scanning dependencies of target conf
[100%] Generating avrdude.conf
[100%] Built target conf

Build succeeded.

Run

sudo cmake --build build_linux --target install

to install.
$ sudo cmake --build build_linux --target install
[ 93%] Built target libavrdude
[ 98%] Built target avrdude
[100%] Built target conf
Install the project...
-- Install configuration: "RelWithDebInfo"
-- Installing: /usr/local/bin/avrdude
-- Installing: /usr/local/lib/libavrdude.a
-- Installing: /usr/local/include/libavrdude.h
-- Installing: /usr/local/etc/avrdude.conf
-- Installing: /usr/local/share/man/man1/avrdude.1

Note:
avrdude.conf installed to /usr/local/etc

REFERENCES
ATMega Specifications (also see “Memory Programming - Fuses”)
https://ww1.microchip.com/downloads/en/DeviceDoc/ATmega48A-PA-88A-PA-168A-PA-328-P-DS-DS40002061A.pdf

Simple blink.hex:
https://github.com/tzhenghao/blink-ATmega328p/blob/master/blink.hex

Negating USB-TTL outputs; programmer configuration:
https://learn.adafruit.com/ftdi-friend/programming-blank-avrs

Command-line parameters:
https://avrdudes.github.io/avrdude/

Building the latest avrdude:
https://github.com/avrdudes/avrdude/wiki/Building-AVRDUDE-for-Linux


Ubuntu comes with cp210x drivers already enabled:
https://askubuntu.com/questions/941594/installing-cp210x-driver




 
