# Fcmila / Sunyesmart SYF05/07 RGBW Wifi Light Bulb

I received it as a gift from a friend, as an interesting gadget
that is not trivial to get operational in a local infrastructure, 
without the vendors servers, accounts, Alexa, HeyGoogle, etc.

Most of the work is already done by the Tasmota project, so all I
needed to do is to add support for the RGB LED controller chip SM16726.

It uses the same synchronous protocol as its predecessor SM16716, so
throughout the code I'll call it '16716'.

For the details of the bulb please see the photos.


## Code quality disclaimer

I have some years of experience with embedded things, but that
means bare-metal and mostly assembly, sometimes C, and definitely not
any kind of 'framework' if I have one chance to avoid it.

This is my first encounter with the Tasmota codebase, 
most probably I haven't done it in the most 'tasmotic' way, so a code review
from an experienced Tasmota developer is more than welcome,
as is any constructive feedback from any fellow developer.

My additions are in [my forked repo](https://github.com/gsimon75/Sonoff-Tasmota/tree/sm16716).

The commits are not squashed together, because now it's still well possible
that some parts'll need to be reverted, but others are to be kept.

If/before it gets integrated, I'll certainly tidy it up, but now it's
just a working copy, please read it as such.

I don't want to litter the codebase with all these bulb-specific informations,
but I don't want to discard them either, so that's why this
separate document.


## Useful links

- [The Bulb](https://www.flipkart.com/fc-mila-bxav-xs-ad-smart-bulb/p/itmf85zgs45fzr7n)
- [TYWE3S wifi module](https://docs.tuya.com/en/hardware/WiFi-module/wifi-e3s-module.html)
- [SM16716 RGB LED Controller](http://www.datasheet-pdf.com/PDF/SM16716-Datasheet-Sunmoon-932771)


## Mechanical properties of the bulb

The bulb chassis consists of four parts:
- the mains connector part (E27 in my case)
- a plastic item that joins the mains connector to the heatsink
- the aluminium heatsink body
- a translucent plastic bulb cover

Within the chassis there are two PCBs:
- the controller
- the LED board


### Mains connector

It is hollow, only the metal casing gives its structural strength, so
don't apply too much force to it.

In case of E27, one wire goes to the central electrode and the other
is just pressed between the threads of the metal E27 casing and the
corresponding threads of the plastic joiner.

The metal casing is screwed on the joiner, and it's point-pressed on
it at 6 places around its circumference.


### Plastic joiner

The narrower end has E27 threads that match the mains connector casing,
and the broader end connects to the heatsink with a threaded joint,
that is also glued together.

The glue between those threads holds firm, so at first I couldn't even loosen it.

There are two (on other models three) small holes just below the
heatsink, most probably to prevent air pressure buildup when the
thing starts getting hot.

On some forum I've read that someone could just poke two small
screwdrivers through these holes and unscrew the joiner from the
heatsink, but in my case the PCB was just across, so I didn't
drill to it just to give it a try :).


### Heatsink

It's made of aluminium and it's ribbed on the outside.

On the inside it seems to be assembled from two parts:

- lower heatsink whose inner diameter is 30 mm and has 3 grooves on the
  inside at even angles, it has an extrusion at the top with outer diameter of 35 mm
- top part, glued to that extrusion

As the PCB is right in the middle, so the grooves serve no purpose here.


### Translucent plastic bulb

Connects to the top of the heatsink by a very shallow groove at its circumference.


### Controller board

Contains the power supply, the TYWE3S wifi module, and something that
looks like a charge pump (2 x 47uF 25V capacitors and an inductor).

It connects to the LED board via a 2 x 4 pin PCB connector.


### LED board

Aluminium-based PCB, contains the SM16726 RGB LED controller, the SLM211A
PWM LED controller, 6 x (2+0) cold white LEDs and 6 RGB LEDs.

For each 2 white LEDs there is an empty place for a 3rd one, these 3 LEDs
are connected in parallel, and the 6 such packs are connected in series,
the anode of the chain wired to Vcc, the cathode being controlled by the
SLM211A pin 7.

The channels of the 6 RGB LEDs are connected in series, with the greens
having 3 additional 100 Ohm resistor in the chain as well. The anode
is wired to Vcc, the cathodes are controlled by the SM16726.

The LED board is glued on the upper part of the heatsink. The contact area
is a ring, the outer diameter is of the LED board: 44 mm, the inner diameter
is of the heatsink: 30 mm, so it's just a 7 mm wide strip around.


## Disassembly

First of all, the plastic bulb can be easily pried away from the heatsink:
push a flat screwdriver or knife between them, twist slowly, and go around
it like this.

Then comes the hard part: to remove the controller PCB.

One method would be to unscrew the plastic joiner from the heatsink, that
would tear the mains wires (no problem, easy to resolder), but in my case the
glue in the threads seems stronger than the plastic.

So I chose the long path:

1. With a needle I scraped away as much of the glue around the LED board as I could.

2. Sawed around the mains connector casing where it is point-pressed to the joiner

3. The outer wire was just pressed in the threads, the inner one was long enough
   to push aside the mains connector cap.

4. Next to the controller PCB I poked a screwdriver through to the LED PCB.
   Don't do it, pushing at one point will deform the LED PCB. Instead of a
   screwdriver poke through 2mm wooden skewers, as many as possible, so the
   pressure will be delivered more evenly.

5. Pushed the LED PCB off the heatsink, it also pulled the controller PCB along
   with it.


To make re-assembling easier, I wanted to separate the heatsink from the joiner anyway.
Forcing them wouldn't work, because the joiner is a soft plastic item and it would
deform sooner than the glue lets it go.

At least at room temperature :). So:

1. Put the glued parts in the deep freezer, cool them to -10 &deg;C
2. Boil a glass of water, enough to sink at least half of the heatsink into it
3. Prepare a kitchen towel and a plier that's large enough to grab the joiner part
3. If the parts have cooled down, take them out and **immediately** put the heatsink part into the boiling water, heating it to 100 &deg;C
4. The joiner is made of plastic, it's a bad heat conductor, so for a few seconds it'll stay cold, while the heatsink will heat up very quickly.
5. If you feel that the heatsink is hot even at the joiner, yank it out of the water, grab the heatsink with the towel, the joiner with the plier, and unscrew the joiner from the heatsink

### The math :)

Aluminium has a linear thermal expansion coefficient of 23.1 * 10<sup>-6</sup> 1/K, that means that if we heat it from 23 &deg;C room temperature to 100 &deg;C, it means a &Delta;T = 77 K, so the expansion factor will be a<sub>hot</sub> = 1 + &alpha; * &Delta;T = 1.0017787

The joiner is some kind of soft plastic, maybe polyethylene, let's assume its &alpha; = 150 * 10<sup>-6</sup> 1/K, so if we cool it down to -10 &deg;C, it means a &Delta;T = -33K, so the contraction factor will be a<sub>cold</sub> = 1 + &alpha; * &Delta;T = 0.995

If the common radius on room temperature is R = 16 mm, then the hot external radius will R * a<sub>hot</sub>, the cold inner radius will be R * a<sub>cold</sub>, so the gap will be R * (a<sub>hot</sub> - a<sub>cold</sub>) = 16 mm * 0.0067787 = 0.108 mm

That 0.1 mm is just enough to stretch that glue film between the threads, so it'll let go :).


## Wiring

[Here](https://community.home-assistant.io/t/cheap-uk-wifi-bulbs-with-tasmota-teardown-help-tywe3s/40508/37)
is the real thing!

- Pin7 (left, 7th from top) is GPIO13, here comed the LED if you want one (*optional*)
- Pin8 (bottom-left) is Vcc=3.3V
- Pin9 (bottom-right) is GND
- Pin12 (right, 5th from top) is GPIO0, here comes the 'button'
- Pin 15 (right, 2nd from top) is RxD, that goes to the TxD of your serial adapter
- Pin 16 (top-right) is TxD, that goes to RxD of your serial adapter

If you want a LED (blinks nicely when booting special modes), connect a LED in
series with a 1 kOhm resistor and connect them between GPIO13 (Pin7) and GND.

You'll definitely need a button, connect it between GPIO0 (Pin12) and GND.

So, at minimum you'll need 5 wires hooked up the module: Vcc and GND, Rx and Tx, and GPIO0.

Remember: the TxD of the module goes to the RxD of the adapter, and the TxD of the
adapter goes to the RxD of the module.

For safety, you don't want to poke around the module when it's connected to the mains, so you'll need to provide a Vcc = 3.3V power supply to it.

During normal operation, the current is around 35-40 mA, but during flashing it peaks at around 60 mA, and while the wifi is connecting, it is around 75 mA, so you'll need to design for at least 100-150 mA.

The built-in 3.3V regulator of the Prolific PL2302 usb-serial chip can supply 150mA, that may even be enough for the board, if there are no relays to switch.

On the other hand, an FTDI2232 chip is certified to supply only 5 mA (yes, five), so you're better off with an external 3.3V regulator.

I recommend AMS1117, it's a little 3-pin beastie that supports up to 1A, so it supplies that average 50-70 mA even without a heatsink pad.


### Having the serial connected while on mains

Discouraged, first for your safety, second for your computers safety, and finally for your bulbs safety.

If everything goes well (that is: *in theory only*) it would be enough to disconnect only the Vcc, so that the boards power supply won't fight with yours, and then you could see the serial log of a live bulb.

Why is it discouraged then?

- It's needed only when you're debugging the actual power LEDs, because *everything else* (including the wifi and the LED drivers)
  *runs just fine from your* safe USB-5V-to-3.3V regulated *power supply*. Don't take risks if you don't even have to.

- During debugging, the board is open to your touch, to dangling wires, to leftover screws rolling around, etc.

  - If you're the one who touches the mains, you'll get at least shocked, at most electrocuted to death. This is no joke.

  - If some dangling wire gets to the mains, it'll fry everything in its path towards the ground. Your usb-serial adapter
    is *not* galvanically decoupled (unless you're military or paranoid enough or both), so this'll fry your whole computer.
    Not just 'might', but 'will'.

  - If only some leftover screw gets there and makes a short circuit, then the breakers will go off, you'll need to
    disconnect everything and switch the breakers on again without electricity in your apartment. Besides, this will fry at least
    the bulb you're fiddling with. Considering the direct cause, we may call it 'it gets screwed' :D.

- The power supply of the bulb is usually far from perfect, so it produces not a nice flat 3.3V,
  but usually quite a storm of short-time but high-voltage peaks, spikes on it. Usually 'only' 
  30-40V of them, but when the LEDs start blazing at full power, it may get even higher.
  The module deals with it (to some extent for some time...), but your serial adapter won't necessarily filter it out,
  so even under normal operational circumstances it *can* pass high-voltage spikes to your computer.

- Again: With accidents, whatever 'can' happen 'will' happen, and the question is not 'if', but 'when'.
  Yes, if you *can* cause unintentionally whip a wire into the high-voltage parts, then you eventually *will*.

- How? Just toss your serial converter away a bit with your mouse, the serial wires move,
  one of your temporary solderings lets off, and there you have an open ended loose wire in the middle
  of the board, whipping around where its elasticity drives it to. Rule of Murphy: It'll go where it
  can cause the most damage. Wanna guess where it is?

Rule of Thumb: Consider it as if a 3-year-old kid and a cat could get to it whenever you just look away.
If it's not safe for them, then it's not safe for you either.

'Nuff said, you get the idea, do your best to stay alive.


## Building Tasmota without an IDE

I'm not a fan of these shiny IDEs, prefer to work on some VM in the cloud,
so here's how to do it. It may be even simpler than the IDE :).

1. Provision a Linux VM, the distribution doesn't really matter, use your favourite one.

2. Install `python` (2.7) and `python-virtualenv`, and `python-pip` because we don't want to mess up the distro. Update pip by `pip install --upgrade pip`, and this was the last step done as root, the rest goes as a plain user.

3. Prepare a PlatformIO-Core environment contained in a folder
```
virtualenv platformio-core
cd platformio-core
. bin/activate
pip install -U platformio
```

4. Fetch the Tasmota sources
If you want only to build, then the original repo will do, but if you want to contribute as well, then fork an own copy of the repo and clone out that one.

```
git clone https://github.com/gsimon75/Sonoff-Tasmota.git
cd Sonoff-Tasmota
```

5. Configure the sources

In `platformio.ini` choose the environment (or flavour, if you like) you want to build.

In `sonoff/my_user_config.h` finetune the default values for the module, the wifi, the MQTT server, and so on. Refer to the Tasmota Wiki for details.

Initialise the build system:
```
pio init
```

6. Build the firmware

```
pio run
```

That's all, really!

PlatformIO seems to handle the rebuilds and dependencies well, but if you want a clean build, the say `pio run -t clean` first, and then the `pio run`.

The result will be here: `./.pioenvs/sonoff/firmware.bin`


7. On your local machine install the firmware uploader

Python again, so install and update virtualenv and pip if you haven't already done so.

```
virtualenv esptool
cd esptool
. bin/activate
pip install esptool
```

That's it.


8. Back up the original firmware 

First of all, disconnect the bulb from the mains and wire up the serial connection *and* a button on GPIO0.

If this GPIO0 is connected to GND when the module gets power, it starts in a firmware-update mode, and you can then read/write its flash storage.

Switch off the power of the board, this will be the reference steady state of the system.

Before everything, make a backup of the original!

Within that 'esptool' virtualenv (don't forget to 'activate' it) :

```
esptool.py read_flash 0x00000 0x100000 fcmila_bulb_orig.bin
```

Now, it'll wait for the module to appear connected, so

- press the button (GPIO0 to GND), *keep it pressed*
- switch on the power of the board
- now you may release the button

If all is well, the flash is being dumped, it may take a minute or so.

If done, then *power the module off*, as this management mode is not restartable!

If it's not well, then you may try some queries:

```
esptool.py -p /dev/ttyU0 chip_id
esptool.py -p /dev/ttyU0 flash_id
```
If they don't work, then check your cabling and your serial adapter (see a rant on cheap clones below).



9. Erase the flash

The next step is to erase the flash. Only if you could make a backup, because if it didn't work, then this won't, either.

```
esptool.py erase_flash
```

(With the usual button-pressed-power-on rain dance, and don't forget to power the module off afterwards.)


10. Install the firmware to your module

```
esptool.py write_flash --flash_size 1MB --flash_mode dout 0x00000 firmware.bin
```

(Power on rain dance before, power off afterwards. I always forget it, you shouldn't :))


11. Power on for normal operation

No button-pressing, power on, and see what you achieved :).

The module sends its logs on the serial line at 115200 baud 8N1,
so to check the logs:

```
cu -s 115200 -l /dev/ttyU0 | tee -a my_sonoff.log
```

(Assuming that you're using FreeBSD. On Linux you set the speed via `setserial` or `stty`, and then do the dump with `dd`.)


### A word on USB RS232 adapters

There are two big categories: cheap crap and normal ones (may be cheap as well),
the crap won't work and you won't know why, and you'd suspect everything else
but the serial adapter.

Here's the reason.

Normal [3.3V CMOS logical signal voltages](https://learn.sparkfun.com/tutorials/logic-levels/33-v-cmos-logic-levels) are:

- '0' is sent as 0V and expected as < 0.8V
- '1' is sent as 3.3V and expected as > 2V 

On the other hand, standard [RS232 voltage levels](https://www.sparkfun.com/tutorials/215):

- '0' is sent as +12V and expected as >= +3V
- '1' is sent as -12V and expected as <= -3V

Therefore the final stage of the *normal* adapters is a voltage level converter
(eg. Maxim MAX232) chip which contains a CMOS-to-RS232 channel and an RS232-to-CMOS
channel.

Now, the normal setup would be:

- your USB serial adapter has a native CMOS-level interface
- then comes its level converter
- then comes the level converter of the device
- and finally the native CMOS-level interface of the device

In this setup the two middle stages, the forth-and-back level converters are unnecessary,
the native CMOS-level interfaces of the adapter and the device can be connected (TxD of
one side to RxD of the other side, and vice versa).

Exactly this is the case with normal 'CMOS-level' USB-to-serial adapters: they lack the
level converter, expose their native CMOS-level interface, and that's what you need.

The problem with the cheap clone adapters is the following:

The manufacturers don't spend on a correct voltage level converter, but
add a simple CMOS logic-level inverter (just swaps the signal: 1->0, 0->1) instead of it:

- '0' is sent as 3.3V and expected as > 2V 
- '1' is sent as 0V and expected as < 0.8V

If you compare this with the standard RS232 levels, you'll see that
- when this thing receives a correct signal, it is accepted correctly:
  - 0: sent +12V matches expected > 2V
  - 1: sent -12V matches expected < 0.8V
- when this thing sends a signal, it is almost correct:
  - 0: sent 3.3V matches expected >= +3V
  - 1: sent 0V *doesn't match* expected <= -3V

Most RS232 receivers tolerate even this, so these junk clones do sell, unfortunately.

How is this relevant to our case?

These clones have CMOS-level signals, but *their signals are inverted* !
A correct CMOS-level TxD signal has 3.3V when idle, these clones have the opposite: 0V.

Guess what happens when you connect such a junk to your module... When some traffic
starts going on, the level moves between 0 and 3.3V, and the other side eventually
will see *some* inbound data, but definitely not what has been send.

Your voltmeter will show CMOS-level voltages, data seems to flow, but it is all garbage,
that will happen.

Remember: Idle TxD shall be 3.3V, otherwise that adapter is a junky one.

