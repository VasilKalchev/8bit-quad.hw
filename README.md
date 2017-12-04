# 8bit-quad hardware

Flight controller board for the 8bit-quad quadcopter. An ATmega328P at 16 MHz,
an nRF24L01+ radio, an MPU6050 or MPU9250 on headers, four ESC outputs, a
battery sense divider, switched LED and lamp outputs, and indicator LEDs.
A 113 mm octagon on two layers, shaped to the top plate of an F450 frame
and etched at home with photoresist.

The firmware lives in [8bit-quad](https://github.com/VasilKalchev/8bit-quad).

> [!WARNING]
> **This is the Eagle design and it is obsolete. Use the KiCad conversion at
> the tip of `main`.**
>
> Autodesk has discontinued standalone Eagle and folded it into Fusion, so
> these files are effectively unopenable today. They are kept as the
> historical source and nothing more, and they are deleted at the tip, so
> they exist only in this commit.

They are complete and self-contained if you do have Eagle: every symbol and
footprint the design uses is embedded in the `.sch` and `.brd`, so the custom
libraries they were drawn from are not needed to open, edit or plot them.

```
fc/8bit-quad-fc.sch    schematic, 128 parts, 39 nets
fc/8bit-quad-fc.brd    board, 64 elements, two layers
fc/layout2ps.cam       output job, PostScript for the photoresist process
fc/_export/            board and schematic plots, exported 2017
rc/                    what the remote had to provide
gallery/               photographs
```

There are no gerbers and never were. The board was made from the PostScript
that `layout2ps.cam` produces, printed onto transparency.

## The remote

There is no board design for it. The remote was hand wired on protoboard and
nothing was ever drawn for it, so `rc/` records what that hardware had to
provide, read back out of the firmware.

> [!NOTE]
> The lettering in these files was changed in 2026 to read `Flight Controller
> v2.0`, which is what the physical board says. As found, they lettered it
> `MaD-FC-2` / `r1.0.0`, a rename made after the board was already etched.
> Nothing else was touched.
