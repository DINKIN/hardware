# The Murgen Project
_The hardware design process is fraught with pitfalls, from library component sketchiness, parts availability, erroneous data sheets, underestimates of complexity and long lead times. Designing good hardware on time, on budget and to specifications is like being in a knife fight. Hardware will cut you._

Desc TBD

# Project introduction
## Brief
In October 2015, we were working on a open-source ultrasound imaging device, and had started to get some result. However, we were facing a bottleneck that is data acquisition and transfer.

Roughly, the aim of the device is to excite a 3.5MHz piezo through a 100V or more, 0.2µs, signal to acquire a signal coming from the echoes.
Once it comes back from the transducer, one needs to cap it, amplify it, filter it, apply a time variable gain to the signal, have it get through a high speed ADC (40MHz at least), and serve it to the the processing unit (through PINs, SPI, USB ?), possibly with a CPLD/FPGA (or DSP / microcontroler ?) in between.

As the data we have should have at least 16 imgs / sec, with 64 lines, the rate shall be around 120Mb/s, and to avoid the data transfer bottleneck (see Estimating datarates), we were thinking of leverage the capacities of raspberry (or beaglebone) and such to have shields/capes that can be used and connect through SPI for example to the raspberry where raw data can be processed, and image can be going out, hence a lower rate.

## What is echOpen ?

TBD.
 
## Preliminary questions
### Why are we making this?
Because it’s fun, it the first step towards medical open hardware based on ultrasounds, so it’s an element for echOpen !
And because existing hardware doesn’t come in for low-cost, rough uses.
### Who is this for?
This element, comprised of the analog part of ultrasound imaging processing, is aiming hackers, makers, … all who can have fun prototyping a low-cost ultrasound device.
### How will this be used?
This shall be used in conjunction with the mechanical unit (motor, encoder, transducer), and plugged into a small processing unit (aka Raspberry, or BeagleBOne Black for exemple)
### What features does it need to have (now)?
Be able to emit a ultrasound pulse at high voltage, and receive a signal, clean it, detect the envelop and ADC it.
### What features does it need to have (later)?
Shifting envelope detection to software once we’re sure the processing works at “low” rates.
### What are the legacy requirements?
Nothing yet, except a couple of constraints on the use of the tool.
### Who’s going to build this?
Makers, doers, academics, … can use this. But we’d be happy to go with a PCB assembler at first!
### How many do we want to make?
At first, a couple, to prototype it. Once prototyped, this board could be in the hundreds.
### What is the timeline?
BBB investigation had started in September 2015, but following a meetup at echopen’s HQ in Paris end of November, it had been activated again.
**The objective is to get a first functional prototype by February 2015.**

## Technically
### System Block Diagram
### Discussion of Essential Features/Trade-offs
### Block-by-Block Breakdown
### Function
### Schematic block
 
## Parts, ICs selection (and critical specs)
 
### HV7360:  High Speed, ±100V 2.5A, Two or Three Level Ultrasound Pulser
* High density integration AC coupled pulser
* 0 to ±100V output voltage
* ±2.5A source and sink minimum pulse current
* Up to 35MHz operating frequency
* 2.0ns matched delay times
* 2.5, 3.3 or 5.0V CMOS logic interface
* Low power consumption and very simple to use

HV7360 is a high voltage, high-speed, pulse generator with built-in, fast return to zero damping FETs. This high voltage and high-speed integrated circuit is designed for portable medical ultrasound image devices, but can also can be used for NDT and test equipment applications.

The HV7360 consists of a controller logic interface circuit, level translators, AC coupled MOSFET gate drivers and high voltage and high current P-channel and N-channel MOSFETs as the output stage. The peak output currents of each channel are guaranteed to be over ±2.5A with up to ±100V of pulse swing. The AC coupling topology for the gate drivers not only saves two floating voltage supplies, it also makes the PCB layout easier.
### Protection: MD0100 Datasheet - HV Protection T/R Switch
* Up to ±100V input voltage protection
* Low on resistance - 15O typical
* Fast switching speed
* Effectively, a simple two terminal device
* No external supplies needed

MD0100 is a high voltage, two terminal, bi-directional, current-limiting protection device. The two terminals are interchangeable. It is designed to protect a low noise receiver from the high voltage transmit pulses in ultrasound applications and is commonly referred to as a T/R (transmit and receive) switch. The MD0100 can be considered as a normally closed switch with a typical switching resistance of 15O that allows small signals to pass.

Once the voltage drop across the two terminals exceeds a nominal value of ±2.0V, the device will start to turn off. In the off state, the MD0100 can withstand up to ±100V across its terminals.

A small amount of current, typical of 200µA, is allowed to flow through.
 
### TGC : AD8331 (Single VGA with Ultralow Noise Preamplifier and Programmable RIN)
* Ultralow noise preamplifier
* 3 dB bandwidth: 120 MHz
* Low power: 125 mW/channel
* Wide gain range with programmable postamp (-4.5 dB to +43.5 dB in LO gain mode, 7.5 dB to 55.5 dB in HI gain mode) – we can use this with the corresponding on-board jumper
* Low output-referred noise: 48 nV/vHz typical
* Active input impedance matching
* Optimized for 10-bit/12-bit ADCs : perfect for us
* Selectable output clamping level
* Single 5 V supply operation : ideal for a 5V board supply!

The AD8331 is a single channel, ultralow noise, linear-in-dB, variable gain amplifier (VGA). Optimized for ultrasound systems, it is usable as a low noise variable gain element at frequencies up to 120 MHz.

Included is an ultralow noise preamplifier (LNA), an X-AMP® VGA with 48 dB of gain range, and a selectable gain postamplifier with adjustable output limiting. The LNA gain is 19 dB with a single-ended input and differential outputs. Using a single resistor, the LNA input impedance can be adjusted to match a signal source without compromising noise performance.

The 48 dB gain range of the VGA makes these devices suitable for a variety of applications. Excellent bandwidth uniformity is maintained across the entire range. The gain control interface provides precise linear-in-dB scaling of 50 dB/V for control voltages between 40 mV and 1 V. Factory trim ensures excellent part-to-part and channel-to-channel gain matching. Differential signal paths result in superb second- and third-order distortion performance and low crosstalk.

### TGC  Control : SPI DAC : MAX5383
Controls the TGC
* 8-Bit Resolution in a Miniature 6-Pin SOT23 Package
* < 1µA Shutdown Mode
* Low-Glitch Power-On Reset to Zero DAC Output
* 3-Wire SPI/QSPI/MICROWIRE-Compatible Interface : perfect for a SPI interface
* Low 230µA (max) Supply Current

The MAX5383 low-cost, 8-bit digital-to-analog converters (DACs) in miniature 6-pin SOT23 packages have a simple 3-wire, SPI™/QSPI™/MICROWIRE™-compatible serial interface that operates up to 10MHz. The MAX5383 has an internal +2V reference and operates from a +2.7V to +3.6V supply.

The MAX5383 require an extremely low supply current of only 150µA (typ) and provide a buffered voltage output. These devices power up at zero code and remain there until a new code is written to the DAC registers. This provides additional safety for applications that drive valves or other transducers that need to be off on power-up. The MAX5383 include a 1µA, low-power shutdown mode that features software-selectable output loads of 1kO, 100kO, or 1MO to ground.

### Analog envelop detection: ADL5511 - DC to 6 GHz ENVELOPE AND TruPwr™ RMS Detector
* Envelope tracking RF detector with output proportional to input voltage
* No balun or external tuning required
* Input power dynamic range of 47 dB
* Input frequency range from dc to 6 GHz
* 130 MHz envelope bandwidth
* Envelope delay: 2 ns
* Single-supply operation: 4.75 V to 5.25 V

The ADL5511 is an RF envelope and TruPwr™ rms detector. The envelope output voltage is presented as a voltage that is proportional to the envelope of the input signal. The rms output voltage is independent of the peak-to-average ratio of the input signal.
The rms output is a linear-in-V/V voltage with a conversion gain of 1.9 V/V rms at 900 MHz. The envelope output has a conversion gain of 1.46 V/V at 900 MHz and is referenced to an internal 1.1 V reference voltage, which is available on the EREF pin.
The ADL5511 can operate from dc to 6 GHz on signals with envelope bandwidths up to 130 MHz.

### ADC : AD9220 - Complete 12-Bit, 10.0 MSPS Monolithic A/D Converter
* Monolithic 12-Bit A/D Converter Product Family
* Flexible Sampling Rates: 1.5 MSPS, 3.0 MSPS, and 10 MSPS
* Single +5 V Supply
* Out-of-Range Indicator
* 28-SOIC and 28-SSOP

The AD9221/AD9223/AD9220 combine a low cost, high speed single-CMOS process and a novel architecture to achieve the resolution and speed of existing hybrid and monolithic implementations at a fraction of the power consumption and cost. Each device is a complete, monolithic ADC with an on-chip, high performance, low noise sample-and-hold amplifier and programmable voltage reference. An external reference can also be chosen to suit the dc accuracy and temperature drift requirements of the application. The devices use a multistage differential pipelined architecture with digital output error correction logic to provide 12-bit accuracy at the specified data rates and to guarantee no missing codes over the full operating temperature range.

The input of the AD9221/AD9223/AD9220 is highly flexible, allowing for easy interfacing to imaging, communications, medical, and data-acquisition systems. A truly differential input structure allows for both single-ended and differential input sample-interfaces of varying input spans. The sample-and-hold (SHA) amplifier is equally suited for both multiplexed systems that switch full-scale voltage levels in successive channels as well as sampling single-channel inputs at frequencies up to and beyond the Nyquist rate. Also, the AD9221/AD9223/AD9220 is well suited for communication systems employing IF Down Conversion since the SHA in the differential input mode can achieve excellent dynamic performance far beyond its specified Nyquist frequency2.

A single clock input is used to control all internal conversion The digital output data is presented in straight binary format. An out-of-range (OTR) signal indicates an flow condition which can be used with the most significant bit to determine low or high overflow.
 
### Buffers: 74AC541MTC
* Signal isolation is of high value for measurements at this precision (12bit)

The 74AC541 and 74ACT541 are octal buffer/line drivers designed to be employed as memory and address drivers, clock drivers and bus oriented transmitter/ receivers.
 
# Using the Murgen cape
## Software / Firmware Summary
TBD // This part is to be update in a future update, as soon as we get the physical board.
## Typical Application
TBD.
## User’s Quick-Start Guide
TBD // This part is to be update in a future update, as soon as we get the physical board.
## Errata
 
 
# Discussions
 
Pending work 


# People ! 
## Acknowledgement

Murgen, Sofian, Amit, Emmanuel, Florent, Bertrand & PHH, Mike in a way, list goes on !

# License

Licensed under TAPR Open Hardware License (www.tapr.org/OHL)

Copyright Murgen and Kelu124 (murgen@echopen.org , luc@echopen.org)

