

Installing x-board
==================

Welcome to the instructions to installing the x-board project. Here, I'll explain 
how to install the whole project to your C.H.I.P. and how to use the x-board-install 
tool to install the project to your Digispark. I will also briefly touch upon how to
use the x-board tool to drive your x-board Digispark, but most of that should be
self-evident.

Step 1: Installing x-board-package
----------------------------------

First, we need to get the tools on the CHIP. Log in to the CHIP and issue the following
commands:

    sudo apt-get install git
    git clone https://github.com/x-board/x-board-package
    cd x-board-package

Step 2: Upgrading the bootloader
--------------------------------

The bootloader that both genuine Digisparks and most clones come pre-installed with,
isn't compatible with the C.H.I.P. For that reason we need to install a newer version
of the bootloader.

The easiest way to do this is to use a USB hub. I used one of the cheapest hubs off 
AliExpress myself, which you can buy for under $1 and some waiting. However, most USB 2.0
hubs should work. If you don't have a USB hub available or the one you have isn't working,
the alternative is to update the bootloader from another computer, which is a process I'll
describe below.

The process is pretty simple. Just plug the hub into your CHIP and execute the following:

    sudo ./x-board-install digispark bootloader

You will be told to plug in your device, at which point you plug the Digispark into hub.
It should pick it up soon enough and start uploading the new bootloader. The bootloader is
self-installing, so when you're told it's done, leave it plugged in for a couple seconds
longer. The on-board LED will flash when it's done installing itself.

Note: don't forget the execute the statement as root. If you do, it will appear to work,
but it won't be able to find your Digispark, so it will be stuck waiting forever.

### Updating from another computer ###

Alternatively, you can update the bootloader from another computer. There are several options,
which vary in the amount of work it takes.

#### Raspberry pi (or another ARM device) ####

This was tested on a Raspberry Pi 3B, but should work on any ARM device running a 32 bit OS,
or an OS that has multi-arch support. Any other ARM devices can still use the "any linux" method
described below. (Of course, similar to how the CHIP doesn't work, another device might also not
work.)

You can simply use the same method as on the CHIP. (You can skip the first two commands if you
already have git installed or you can substitute the first three commands by downloading
x-board-package in a different way.)

    sudo apt-get update
    sudo apt-get install git
    git clone https://github.com/x-board/x-board-package
    cd x-board/x-board-package
    sudo ./x-board-install digispark bootloader

Then, just plug in the Digispark, and everything should work.

#### Windows ####

Download the x-board-package and put it somewhere on your system. Then run 
`x-board-package/windows/upgrade-bootloader.bat`. Then plug in your Digispark.

#### Any linux system ####

I don't provide a micronucleus binary for any other platforms, so you will have to compile it
yourself. Luckily, that's not too hard. First, make sure that `git` and `libusb-dev` are installed
(e.g. `sudo apt-get install git libusb-dev`, then run the following:

    git clone https://github.com/x-board/x-board-package
    git clone https://github.com/micronucleus/micronucleus
    cd micronucleus/commandline
    make
    sudo ./micronucleus ../../x-board-package/images/bootloader.hex

Then plug in the Digispark and hopefully everything just works.

#### Mac OS X ####

I don't have access to a Mac system, but I'd imagine the process would be similar to the 
"any linux" above. Actually, I have heard that on Mac you can simply execute the last command
without root access and it will still work.
    

Step 3: Identifying your model
------------------------------

The original version of the Digispark had the problem where you had to detach the on-board
LED in order to use I2C. Since we want to do just that, you need to find out which revision
of the Digispark you have.

For an official Digispark, this is simple: just look at the markings on the board. If it
doesn't mention a revision, it's the original version with this problem. If it mentions
"rev 2" or "rev 4", you've got a later version and you're in the clear.

However, all of the clones I have had my hands on, had no indication of a revision, and yet
were all of a later revision. That's why the x-board project includes a surefire way to get
detect which model of the Digispark you have. Simply execute:

    sudo ./x-board-install digispark identify-model

Then, you insert the Digispark into your CHIP. It should work to insert it into the CHIP
directly, now.

Note: once again, if you forget to run this as root, it will run, but not find your Digispark.

After the image has been installed, the on-board LED will either start blinking or just light
up solid. If it blinks, you've got a later model and are all set. If it is lit constantly,
you've got an older version and need to disconnect the LED before you can use x-board. See the
[Digispark wiki](http://digistump.com/wiki/digispark/tutorials/modelbi2c) for how to do this.

If your on-board LED neither blinks nor is on, you have got an older version of which the LED
has already been disconnected, a broken Digispark, or some other unknown version. At this point,
the only thing you can do is see what happens if you continue by installing x-board to the
Digispark.

Step 4: Installing x-board to your Digispark
--------------------------------------------

We simply use the x-board-install tool again:

   sudo ./x-board-install digispark
   
Plug in your Digispark and noec it's done, you should be all set to start using it as an extension 
board.

Note: you still shouldn't forget to execute x-board-install as root. Though it will appear to work
without root privileges, it won't be able to find your Digispark, so it will be waiting forever.

Using x-board
-------------

With the board installed now, you can start using the x-board tool to drive its pins. For power,
either plug the Digispark into the CHIP's USB port, or connect CHIP's VCC-5V to the Digispark's 5V
pin and connect GND to GND. Next, you need to connect the wires for I2C communication. To do this,
connect CHIP's TWI2-SCK to P1 on the Digispark and TWI2-SDA to P0. Then, you can simply run the 
following to see how to use the x-board tool.

    ./x-board

This will give you an overview of how you can call the tool. Most of the options should
self-explanatory, so for now I'm going to leave exploring them to you.

The one exception to that is the "list capabilities" call. It just dumps the response of the device
in a format that isn't human readable. Basically, this call isn't really usable yet.

Don't forget that the Digispark takes 5 seconds to start (the bootloader is waiting for a new program
to be uploaded), so you need to wait those 5 seconds before you can use it. If it still doesn't work
after that, see the known issues below.

Known issues
------------

There are a couple of known issues:

- On Digispark clones, pin 5 doesn't always work. In fact, in these cases even connecting the pin may
  keep the device from working. This is because a fuse ("hardware setting") is different on the clone
  from how it's set on official Digisparks. For now, just don't use or connect pin 5.

- Often, the first command sent to the Digispark after booting the board doesn't work. I thought I had
  gotten rid of this problem, but it still seems to occur, even if it doesn't always happen. If it does,
  just send another command as it seems that all commands after the first failed one work.

