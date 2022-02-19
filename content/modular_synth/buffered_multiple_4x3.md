---
title: "Buffered multiple 4x3"
date: 2022-02-19T20:58:06+01:00
draft: false
coverImage: //modular_synth/buffered_multiple_4x3/buffered_multiple_4x3_v1.jpeg
categories: modular synth
tags:
  - modular synth
  - buffered multiple
  - multiple
  - stripboard
  - 5x20
---

## Abstract

A simple 4x3 buffered multiple which can easily be fit on a `46x16` stripboard.
The schematics are based on the [1161 Buffered
Multiple](https://www.lookmumnocomputer.com/projects#/1161-buffered-multiple)
from Sam Battle (*aka* LOOK MUM NO COMPUTER).

## Introduction

Alright, this is a very simple yet absolutely essential module in a modular
synth: the buffered multiple.

Why is it called *multiple*? Because it's makes *multiple* signals out of one by
replicating it. What does *buffered* mean? In contrast to a passive multiple
which is basically a cable distributor, a buffered multiple not only replicates
the signal on each of its outputs, it also *buffers* them in the sense that it
decouples it from the original current source. This essentially means that you
can feed in a high impedance signal (which effectively means its voltage would
collapse when it's drained too hard) and you get a low impedance one.

This is a 4x3 multiple, so it provides 4 inputs and 3 outputs for each input.
The circuit is fairly simple and consists of four quadruple opamps (`TL074`).
Three of the opamps of each `TL074` are used to buffer the outputs and one is to
drive the corresponding LED. I recommend bipolar LEDs which will light up in
different colours depending on the polarity of the voltage. It's very useful
(and cool looking) when you buffer for example LFO signals, so that you can see
when the voltage is positive or negative. Be aware that LEDs have a forward
voltage of usually a little more than one 1V (depending on the type), so you
won't see a any light when the signal is lower than that.

## Stripboard Layout

Below is the stipboard layout which can also be split in half easily in case you
are a Eurorack builder or only want to build a 2x3. You can simply solder a 5x2
pin-header on the top 5 stripes and a female connector on the second board, so
that you can connect the two halves. This method can also be used to make a 8x3
multiple and so on `;)`

PDF version of the stripboard layout: [Buffered multiple 4x3 stripboard v1 (PDF, 427KB)](/modular_synth/buffered_multiple_4x3/buffered_multiple_4x3_stripboard_v1.pdf)

![Buffered multiple stripboard layout v1](/modular_synth/buffered_multiple_4x3/buffered_multiple_4x3_stripboard_v1.jpeg)

## Panel

I made a `5cm x 20cm` panel which is a perfect fit for my taste. I use 2mm
aluminium sheets with the following panel layout:

![Buffered multiple panel layout v1](/modular_synth/buffered_multiple_4x3/buffered_multiple_4x3_panel_layout.png)

...and here is the finished panel with all the components mounted:

![Buffered multiple panel](/modular_synth/buffered_multiple_4x3/buffered_multiple_4x3_panel.jpeg)
