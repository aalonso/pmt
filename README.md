# NXP Power Measurement Tool for i.MX Processors
![pmt](https://user-images.githubusercontent.com/52401665/88897649-f13e6600-d24b-11ea-8a93-c993e326e10b.PNG)

## Introduction

Power Measurement Tool will provide general control of iMX8 development boards (reset, Boot mode, IO control), collect on-board power information and provide reports to user. It integrates a user-friendly GUI with many features based on pyqtgraph and PyQT5.
This program runs with python3 under Linux and Windows.

## Installation
### Linux:
The following packages must be installed for the tool to start:
 - pylibftdi
 - numpy
 - pyqtgraph
 - pyqt5
 - pyftdi
 - xlrd

The installation of all dependencies can be done by:
 - pip3 install -r requirements.txt
 - sudo apt install python3-pyqt5 libftdi1-dev

### Windows:
**Firstly install python 3.x version (at least 3.5.4) available at https://www.python.org/downloads/.**
**During installation, click on "add Python 3.x to PATH" and check version after installation.**

The following packages must be installed for the tool to start:
- pip3 install windows-curses
- pip3 install ftd2xx
- pip3 install numpy
- pip3 install pyqtgraph
- pip3 install pyqt5
- pip3 install pyftdi
- pip3 install xlrd

Install the FTDI D2XX library available here: https://www.ftdichip.com/Drivers/D2XX.htm
Copy the files in the PMT directory.

For Windows, please use command **"python"** instead of **"python3"**.
______________________________________________________________________________________________

## Run PMT

To access FTDI FT4232h chip, one would either need to run PMT as a superuser (not recommended, errors may occur), or use the following procedure,
adding specific udev rules to allow FTDI chip access to members of the dialout group (or any other groups you may chose):

1- Create a file named /etc/udev/rules.d/90-ftdi.rules, containing the following line:

SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6011", GROUP="dialout", MODE="0660"

2- Then reload the udev rules:

% sudo udevadm control --reload-rules

% sudo udevadm trigger

3- Add your user account to the standard dialout group:

% sudo usermod -aG dialout $(echo $(whoami))

4- logout of your machine, and login again.


$ python3 main.py [-options]

By default, the program will probe every available power rail. You can choose the rail / group you
want to read by modifying the file program_config.py.

**Notes:**

- Program will automatically detect connected board(s). If 2 boards or more are connected, please specify at least board name or loc_id provided by lsftdi.

- In case any other FTDI USB chips are connected to the Host PC, user must ensure they are also registered in 90-ftdi.rules.

  To do so, add new udev rule entry with correct idProduct attribute. E.g. for FT2232, add the following new rule in 90-ftdi.rules:

  SUBSYSTEMS=="usb", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6010", GROUP="dialout", MODE="0660"

_____________________________________________________________________________________

## Features
### Program features

| commands                 | description                                                                   |
| ------------------------ | ----------------------------------------------------------------------------- |
| lsftdi                   | Show the list of connected boards and the their location IDs                  |
| lsgpio [-b]              | Show a list of available gpio pin of a board                                  |
| lsboard                  | List all supported board models                                               |
| lsbootmode [-b]          | Show a list of available boot mode of a board                                 |
| reset [-b] [-bootm] [-d] | Reset the specified board with possible choosen boot mode and delay           |
| set_gpio [-b] [-g] [-v]  | modify [-g] gpio name with specified [-v] value                               |
| monitor [-b] [-m] [-l] [-i] [-t] [-d]  | display power / voltage / current data in [-m] mode. Possibility to load file |
| eeprom [-m] [-i] [-f]    | Read or Write in FTDI / I2C EEPROM                                            |

#### Examples

**lsftdi:**
$ python3 main.py lsftdi

**lspgio:**
$ python3 main.py lsgpio -b imx8dxlevk

**lsboard:**
$ python3 main.py lsboard

**lsbootmode:**
$ python3 main.py lsbootmode -b imx8dxlevk

**reset:**
$ python3 main.py reset -b imx8mppwrevk -bootm emmc -d 2

**set_gpio:**
$ python3 main.py set_gpio -b imx8mppwrevk -g FT_GPIO1 -v 1

**monitor in GUI:**
$ python3 main.py monitor -b imx8dxlevk -m gui

**monitor in TUI:**
$ python3 main.py monitor -b imx8dxlevk -t 15 -d test.csv

**read EEPROM content:**
$ python3 main.py eeprom -m read

**write in EEPROM with Programmer Tool excel file:**
python3 main.py eeprom -m write -f docs/EEPROM_Programmer_Tool.xlsx

### GUI features

- Export file as .csv, binary .pmt, .png.
- Import file as .csv, .pmt.
- Select Power / Voltage / Current rail to plot.
- Change color of each rail.
- Switch resistor value of each rail.
- Hide / Show extern windows.
- Reset display.
- Pause display.
- Hide all rails plotted with one click.
- Get mouse pointer data informations.
- Zoom region with mean power / mean voltage / mean current data.
- Data since the application starts.
- Average values of plotted rails.
- Stop / resume data acquisition with stop region displayed.
- PAC's bipolar mode.
- PAC's hardware filter.

### TUI features

- Export file as .csv (if no extension is specified in command line).
- Switch resistor value of each rail.
- Reset values.
- Set monitor duration.
- PAC's bipolar mode.
- PAC's hardware filter.


### Monitor mode

In monitor mode (GUI or TUI), you have the possibility to switch between high-current shunt and low-current shunt.
However, depending of the current value and the current average value, the switching is authorized or not.
To secure the chips of the board, an offset is defined in program_config.py (default 10%) and deduce to the current limit.

Example:
If the current limit is 10mA and the offset is default value i.e 10%. The average current value should be
less to 9mA to allow the switching from high-current shunt to low-current shunt.

**The user can modify this offset depending of the needs.**
**Switch between low-current shunt to high-current shunt is always authorized, we don't do any pre-check.**


## Important information

If you use the board for the first time, please ensure that the EEPROM is correctly programmed. If not, program it
thanks to the EEPROM_Programmer_Tool file with the correct information.

**For automatic board detection, PMT reads the content of the EEPROM.**

## Contact

For any question or issue please open an issue in PMT Github (https://github.com/NXPmicro/pmt/issues).
_____________________________________________________________________________________

## License
 BSD-3 Clause "New" or "Revised" License
