---
title: 'Battery Powered Bass Amp'
date: 2026-10-18T16:25:37+01:00
draft: false
tags: ['Bass']
type: post
featured_image: 'img/Updated amp with TPA3255.jpeg'
---

I've been meaning to write this up for several years now, but I've finally got a version of this project that's worth writing up.

The project would also work well for an ordinary (6-string) electric or electro-acoustic guitar, but you might want a preamp with EQ, FX send-return etc. Simplicity, and the need for for bass to have higher power output drove this project direction.

# Requirements
- Run time of several hours - long enough for multiple gigs between charges
- Uses easily available Li-Ion battery packs
- Based on off-the-shelf Class D power amp module
- Goes loud enough for medium-sized gigs

## Nice-to-haves
- Drives 8 ohm speaker loads (at ~70 watts) or 4 ohm speaker loads (at ~140 watts)
- Runs cool enough that the fan hardly comes on

At the end I've included notes on previous iterations, which were lower powered with less-capable modules.

# How does it sound?

At low settings, it sounds pretty neutral/clean. The preamp can give a bit of soft-clipping dirt at higher gain settings.

Sound level is very much down to the speaker cabs that you use it with. A single small, fairly sensitive cabinet such as a [Barefaced One10](https://barefacedaudio.com/products/one_10) goes loud enough to accompany a big-band with a drum kit.

If you need to go louder, The One10 is 8 ohms, so you can add a second One10 in parallel (giving twice the effective watts into an overall 4 ohm load). Or switch to a bigger more sensitive cab.

> [!TIP] The +3 dB rule
> Decibels are logarithmic. To get a noticeable, significant jump in volume (+10 dB, which sounds roughly "twice as loud" to human ears), you actually need _10 times the amplifier wattage_ which is infeasible.
>
> Doubling the amp output watts only nets you a +3 dB increase, which humans hear as _slightly louder_.

So, if you want to go louder, it's better to use [more sensitive (efficient) cabs than increasing amp watts](https://barefacedaudio.com/pages/amp-vs-cab-power).

# Batteries

I use Milwaukee 18V battery packs as I already use them for power tools. (Aftermarket cheaper compatible ones are available on eBay, although folks say they are not as good as genuine ones). The packs have charge status LEDs. Aftermarket adapters (with trailing wires) are also available on eBay.

# Power supply

This allows operation from either one or two 18V batteries.

![Preamp](img/battery_supply_light.png)

Note that if using only one battery, it must be in the "BATT 1" position.

> [!CAUTION] Fuse placement
> Fuses should be as close as possible to the source of the power. My build has fuses in chassis-mounted holders, which is not ideal. I'm careful when plugging and unplugging the power connectors. It would be better to have them upstream in the battery clips, but I didn't have room. My battery packs have over-current/overload, over-discharge (low voltage), and overheating protection but this does not remove the need for  fuses. I take care when plugging and unplugging the power packs to not allow the barrel connectors to touch metal objects etc.
>
> Check your fuses are rated for DC applications.


If Batt 2 trips or is removed, you fall back to 18 V with the amp still working. If Batt 1 trips, everything stops. So the fuller or healthier pack should go in the Batt 1 slot.

# Preamp

This is the Jack Orman [AMZ MOSFET Booster](http://www.muzique.com/schem/mosfet.htm) circuit, built on strip board. It's tiny, and light enough that it can just hang off the back of the gain potentiometer inside the case.

![Preamp](img/MOSFET%20preamp.jpeg)

This is mostly unchanged through the versions of this amp. As the project evolved, I added an LM317 regulator to give a steady 12V rail for the preamp, and made R2 adjustable to set the bias point. I found that setting R2 to about 116 k gave maximum dynamic range, with the onset of clipping at upper and lower output voltage swings to be roughly the same.

# Power amp

TPA3255 module. This can handle supply rail voltages up to 50V, and is happy with 4 ohm loads. It claims to produce 300W+300W, but that's only possible with the maximum supply voltage.

![TPA3255 power amp module](img/TPA3255.jpg)

About £23 from [Amazon](https://www.amazon.co.uk/dp/B0CPP72CQR) at time of writing in 2026 for the ZK-3002 Dollatek branded / Wuhzi Audio board. It's a bit bigger than the previous modules - it's longer, and also higher with a more substantial fan. This required some mechanical rearranging inside the amp box. It came with a [downloadable instruction manual](img/New%20power%20amp%20module%20-%20ZK-3002ins.pdf), English starts on page 11. There's also an English manual I found [here](https://manuals.plus/asin/B0CPP72CQR).

In bridge mode the right and left output channels are parallelled together, allowing the output stages to provide shared current. It supports tougher theoretical speaker loads down to 2 ohms.

Initial tests showed that, into speaker dummy loads, I was getting nowhere near the expected power output. The The MOSFET booster preamp cannot provide enough current to drive the power amp input properly. A buffer stage between preamp and power amp is necessary.

# Buffer stage

Any basic op-amp will work in this circuit; I used a [TL071](https://www.ti.com/lit/ds/symlink/tl071.pdf) which has low input bias current and  quite good output swing.

![TL071 buffer](img/tl071_single_supply_buffer_schematic.svg)

This shows a 9V rail, but it works fine on 12V too.

C4 provides DC blocking on the input. This is connected directly to point A of the MOSFET booster, i.e. skipping its own output decoupling cap.

# Measurement

A 1kHz input tone (cyan) gives clean output maximum swing of nearly 8V pk-pk (yellow) even with a lowly 741 op-amp. A TL071 should do better.

![1kHz input](img/sine_test.png)

Playing the bass guitar open G string shows that there is plenty of gain available from the preamp and buffer.

![Bass guitar open G](img/bass_open_g.png)

Here's a [simple Python program](https://github.com/tuck1s/rigol) used to capture traces from the Rigol scope over the LAN, without that [tedious mucking about with](https://hitchhikers.fandom.com/wiki/Infinite_Improbability_Drive) ancient USB sticks.

The same Python tool can grab a comma-separated variable snapshot of the amp output, enabling us to see a "spectrum analyzer" view of a test tone passing through the preamp and buffer.

![Spectrum](img/1kHz_spectrum.png)

This shows the 1kHz tone, with the first significant sideband at around 48kHz. This is likely to be an artefact of the DAC of the mobile device producing the tone, not the amp.

I found during testing that the buffer output swing was now too much for the power amp input. I replaced R4 with an 820 Ω / 470 Ω divider, which also provided a lower effective output impedance to the power amp.

# Mechanical construction

The power supply is built with point-to-point wiring.

The preamp is held by the gain pot.

The buffer is secured with a zip tie (not shown below - photo was taken during initial testing with a 741 op-amp).

The power amp is held with nylon threaded bushes and screws.

![Updated amp with TPA3255 and buffer](img/Updated%20amp%20with%20TPA3255.jpeg)

Power supply connections are in red + black.

The Speakon connector outputs (blue + brown) are wired in parallel, allowing for easy connection of two speakers.

## Power amp artifacts

The power amp output has some artifacts at around 400kHz. These are at a low level, less than 100mV. This is expected for a Class D output stage.

![Class D artefacts](img/class_D_artefacts.png)

# Possible future work

- Rebuild with a single PCB for power supply, regulator, preamp and buffer
- Better current steering diodes. The IN5401s in parallel are not ideal. The GI752
    carries the full load current (about 4 A average at 140 W into 4 Ω) in two-pack mode, so it
    dissipates around 3.5 W. A silicon diode drops about 0.9 V, so a heat-sinked Schottky would be a
    better choice (e.g. MBR10100G). The 1N5401 pair only carries the single-pack current, and parallel
    silicon diodes don’t share evenly.

# Previous iterations

First version, 2019: TDA7498 module. The module I purchased was limited to 36V absolute max. Fully-charged 18V batteries can reach >20V, so this limited the project to a single battery.

![TDA7498 power amp module](img/TDA7498.jpg)

Second version: TDA7498E. This uprated version has a fan (which hardly ever comes on) and was only £12. It can handle higher supply rail voltages. Unfortunately the one I purchased only had one working channel, so I was unable to try out bridge mode. This can happen with cheap eBay modules sometimes.

![TDA7498E power amp module](img/TDA7498E.jpg)

Third version: TPA3255.