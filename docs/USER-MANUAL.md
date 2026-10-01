# Smart Driver Configurator — User Manual

Every page, every setting, what it does and when to change it.

New to this? Start with the [main guide](../README.md) and come back here
when you want to know what a particular control actually does.

---

## Contents

- [1. Before you start](#1-before-you-start)
- [2. The window](#2-the-window)
- [3. Connecting](#3-connecting)
- [4. Live vs. saved — read this one](#4-live-vs-saved--read-this-one)
- [5. RC / Calibration page](#5-rc--calibration-page)
- [6. Channels & Direction page](#6-channels--direction-page)
- [7. Tuning page](#7-tuning-page)
- [8. Battery page](#8-battery-page)
- [9. Firmware page](#9-firmware-page)
- [10. Themes and settings file](#10-themes-and-settings-file)
- [11. Keyboard shortcuts](#11-keyboard-shortcuts)
- [12. Default values](#12-default-values)
- [13. Troubleshooting](#13-troubleshooting)

---

## 1. Before you start

> 🔴 **The motors are live the whole time the configurator is connected.**
> This is deliberate — it is how you feel each change as you make it. It
> also means the robot will drive off the bench if you let it.
>
> **Wheels off the ground. Every time.**

You also need:

- The USB driver installed (see [Step 2 of the main guide](../README.md#step-2--install-the-usb-driver))
- Firmware already flashed to the board
- Nothing else using the COM port — close the Arduino IDE's Serial Monitor

---

## 2. The window

| Area | What it is |
|---|---|
| **Toolbar** (top) | COM port, baud, **CONNECT**, theme picker |
| **Status strip** | firmware version, link state, live battery voltage |
| **Ribbon tabs** | the five pages. A dot marks a page with unsaved changes |
| **Main area** | the current page |
| **Footer** | Save, Reload and Defaults — these act on the **current page only** |
| **Log** (bottom) | what the app did, and anything the board said back |

---

## 3. Connecting

1. Plug the Nano in by USB.
2. Pick the COM port. **↻** rescans if it is not listed.
3. Click **CONNECT**.

### What happens

Opening the port resets the Nano. The firmware then waits **2 seconds** for
the PC to introduce itself. The app does so automatically, the board
answers, and the app reads every current setting off the board.

You do not have to press anything on the board, hold any button, or get
any timing right.

### How you know it worked

- The status strip shows the firmware version (`v3`)
- All the pages fill in with real values
- The battery voltage starts updating
- **The LED on the robot changes to three blinks and a pause**

### Disconnecting

Click **DISCONNECT**. The board stops the motors, closes the serial port,
and starts running normally.

If you have unsaved changes, the app asks first and tells you exactly which
pages would lose them.

---

## 4. Live vs. saved — read this one

This catches everybody once.

**When you change a control, it is sent to the board immediately.** The
robot responds straight away — that is the point. But it is **not stored
yet**, and a power cycle will lose it.

Storing it is a separate, deliberate action.

| Footer button | What it does |
|---|---|
| **SAVE ⟨PAGE⟩ SETTINGS** | Writes **this page's** settings to the board permanently |
| **Reload tab saved settings** | Throws away live changes, re-reads this page from the board |
| **Default setting for tab** | Resets this page to factory values — live only, not stored until you Save |

**Saving is per page.** Saving the Battery page cannot disturb your radio
calibration. Resetting the Tuning page leaves your mixing alone. The tab
with a dot next to it is the one with unsaved changes.

**So: change, test, then Save.** If you change something and it makes
things worse, press **Reload** rather than trying to remember the old
number.

---

## 5. RC / Calibration page

**What it is for:** teaching the board what your radio actually sends, and
watching what it does with it.

### The live displays

| Display | Shows |
|---|---|
| **Channel bars** | raw pulse width from each channel, in microseconds (µs) |
| **Stick view** | channels 1 and 2 plotted as a 2-D stick position |
| **outL / outR** | what the motors are actually being told to do, −255 to +255 |
| **Armed** | whether the motors are allowed to move at all |
| **Frames** | a counter that ticks up once per radio frame |

**The frames counter is your most useful diagnostic.** If it is not
climbing, the board is not hearing the receiver at all — and nothing else
on this page will work until that is fixed.

**outL / outR is the second most useful.** It shows the result of
calibration, mixing, reversing and the dead band all together. If the
sticks move but these stay at 0, the problem is in your settings. If these
move but the wheels do not, the problem is in the wiring.

### The settings

| Setting | Range | What it is |
|---|---|---|
| **Min** | 700–1600 µs | the pulse width when the stick is at one extreme |
| **Mid** | 900–2100 µs | the pulse width when the stick is centred |
| **Max** | 1400–2500 µs | the pulse width at the other extreme |

Three of each, one per channel.

### How to calibrate

1. **Centre all sticks and trims.** Capture **Mid**.
2. **Move every stick to both extremes**, slowly, including any that you
   use for throttle. Capture **Min** and **Max**.
3. Click **SAVE RC CALIBRATION SETTINGS**.

Do this whenever you change transmitter, change model memory, or adjust
your trims. Bad calibration is the usual cause of a robot that creeps when
the sticks are released.

---

## 6. Channels & Direction page

**What it is for:** how the receiver is wired, and which way everything
goes.

### Receiver protocol

| Option | Use when |
|---|---|
| **3 × PWM** | you have three servo leads from the receiver to D4, D5 and D6 |
| **PPM** | you have one signal wire from the receiver's PPM output to D4 |

PPM carries up to 8 channels on a single wire. If your receiver supports
it, it is the better choice — fewer wires, and D5 and D6 are freed up.

Changing this takes effect immediately, no reboot needed.

### PPM channel map *(only shown in PPM mode)*

| Setting | Range | What it is |
|---|---|---|
| **Channel 1 source** | 1–8 | which PPM channel drives the first input |
| **Channel 2 source** | 1–8 | which PPM channel drives the second |
| **Channel 3 source** | 1–8 | which PPM channel drives the throttle limit |
| **Invert PPM** | on / off | for receivers with inverted PPM output |

In PPM mode an extra live display appears showing **every** decoded
channel, so you can wiggle a stick, see which number moves, and map it.

Leave **Invert PPM** off unless you get no signal at all with the wiring
confirmed good.

### Channel count

| Option | Meaning |
|---|---|
| **2** | steering and drive only. Top speed is fixed by *Max speed* |
| **3** | channel 3 becomes a **live top-speed limit** |

With 3 selected, **the board refuses to drive if it cannot see channel 3.**
That is intentional — a missing throttle channel used to mean "full speed",
which caused a lurch on power-up. If your robot will not arm in 3-channel
mode, this is the first thing to check.

### Mixing

| Option | Meaning |
|---|---|
| **Transmitter** | your radio already mixes. Channel 1 → left motor, channel 2 → right motor |
| **Firmware** | channel 1 = forward/back, channel 2 = turn. The board mixes them |

**Transmitter** suits tank-style control with two sticks. **Firmware**
suits arcade control, where one stick does everything.

With **Firmware** mixing, a hard turn at full throttle keeps its ratio —
both motors scale down together rather than one clipping.

### Direction

| Setting | What it does |
|---|---|
| **Reverse channel 1 / 2 / 3** | flips an input before anything else happens |
| **Reverse left motor** | flips the left motor output |
| **Reverse right motor** | flips the right motor output |
| **Swap motors** | exchanges left and right entirely |

**Use these instead of rewiring.** Watch the **outL / outR** bars on the
RC page while you move the sticks, and work through the flags until it is
right.

- Both wheels backwards → reverse both motors
- Robot steers the wrong way → **Swap motors**
- One wheel backwards → reverse that motor only

### Max speed

| Setting | Range | What it is |
|---|---|---|
| **Max speed** | 1–255 | a hard ceiling on motor output |

255 is full power. Lower it for a beginner driver or a small playing area.

**Ignored in 3-channel mode**, where channel 3 sets the ceiling instead.

---

## 7. Tuning page

**What it is for:** how the board turns stick movement into motor power.

### RC dead band

| Setting | Range | Default |
|---|---|---|
| **RC dead band** | 0–100 µs | **8** |

How far the stick can move from centre before anything happens. With Mid
at 1500 and a dead band of 8, the range 1492–1508 counts as "centre".

- **Robot creeps with the sticks released** → raise it a little, but
  re-running the radio calibration is the better fix
- **Feels like there is a dead spot** → lower it

### Driver dead counts

| Setting | Range | Default |
|---|---|---|
| **Driver dead counts** | 0–150 | **40** |

**This is a property of the hardware, not a preference.**

The BTS7960 limits how fast it switches, which wastes about 5 µs of every
pulse. At 31 kHz a pulse is only 32 µs long, so roughly the first 16 % of
any command produces no movement at all. This setting tells the firmware
how much to skip over.

It depends on the PWM frequency, so **change it whenever you change the
frequency**:

| PWM frequency | Dead counts |
|---|---|
| 31 kHz | **40** |
| 4 kHz | **5** |
| 500 Hz | **1** |

- Too low → the bottom of the stick range does nothing
- Too high → the motor jumps instead of starting smoothly

### Start margin

| Setting | Range | Default |
|---|---|---|
| **Start margin** | 0–100 | **8** |

Extra power on top of *Driver dead counts*, so the very first command is a
little past barely-moving.

**This is the one to adjust by feel.** It is the difference between a
robot that creeps away smoothly and one that lurches.

- **Motor buzzes but does not turn** at the smallest stick input → raise it
- **Robot lurches** off centre → lower it

Change it a few counts at a time and test between each.

### PWM frequency

| Option | Trade-off |
|---|---|
| **31 kHz** *(recommended)* | silent. Drivers run warmer |
| **4 kHz** | audible whine. Drivers run cooler |
| **500 Hz** | rough on the motors. Only for diagnosis |

31 kHz is above human hearing, so the motors are silent.

**Always reset Driver dead counts when you change this** (see the table
above), or the controls will feel wrong afterwards.

### Failsafe timeout

| Setting | Range | Default |
|---|---|---|
| **Failsafe timeout** | 0–5000 ms | **500** |

How long the board will go without a radio signal before stopping the
motors.

Without this, a receiver that loses power leaves its last command sitting
in memory and the robot drives away on its own.

- **500 ms** is a good balance
- **Too low** and ordinary radio glitches cause stutters
- **0 disables it** — not recommended

---

## 8. Battery page

**What it is for:** measuring the pack and stopping before you damage it.

> Battery monitoring is **off by default**, because an uncalibrated scale
> reads the wrong voltage and would cut the motors instantly. Calibrate
> first, then enable.

### Enable battery monitoring

Turns the whole system on. With it off, the board never checks voltage and
the LED stays solid regardless.

### Calibration

The board measures voltage through the R1/R2 divider, but real resistors
have tolerance and the Nano's 5 V rail is never exactly 5 V. So it has to
be told the truth once:

1. Measure the pack with a multimeter.
2. Type that reading into the calibration box.
3. The app works out the scale factor and sends it.

Check it afterwards by comparing the live reading with the meter. They
should agree within about 0.05 V.

### Full voltage

| Setting | Typical |
|---|---|
| **Full voltage** | **12600** mV for a 3S LiPo |

Only used for the gauge display — it is what counts as "100 %".

### Cutoff voltage

| Setting | Typical |
|---|---|
| **Cutoff voltage** | **10500** mV for a 3S LiPo |

Below this, the motors are disabled.

10500 mV is 3.5 V per cell, which is a safe floor. Going below 3.0 V per
cell damages a LiPo permanently.

| Cells | Full | Suggested cutoff |
|---|---|---|
| 2S | 8400 mV | 7000 mV |
| 3S | 12600 mV | 10500 mV |
| 4S | 16800 mV | 14000 mV |

### How the cutoff behaves

It does not cut the instant the voltage dips, because a motor pulling hard
sags the pack briefly and a simple threshold would chatter on and off:

- Voltage must stay low for **1 second** before the motors cut
- Voltage must then recover by **0.3 V for 2 seconds** before they resume

If the motors stutter during hard acceleration, the pack really is sagging
below the cutoff — it is tired, the wiring is too thin, or the cutoff is
set high for that pack.

---

## 9. Firmware page

**What it is for:** flashing a new `.hex` without installing anything.

> **Disconnect first.** Flashing and the serial connection cannot share
> the port. The app will tell you if you forget.

| Control | What it does |
|---|---|
| **Browse** | pick the `.hex` file. It is validated and its size shown |
| **Port** | which COM port the board is on |
| **Baud** | upload speed — see below |
| **UPLOAD** | flashes it, with full progress in the log |

### Which baud rate

| Option | Use for |
|---|---|
| **Auto** | start here — tries 115200, falls back to 57600 |
| **57600** | most clone Nanos (old bootloader). **Try this if Auto fails** |
| **115200** | genuine Arduino and newer boards |

### If it fails

`programmer is not responding` or `not in sync` is almost always the baud
rate. Set **57600** explicitly.

Otherwise: check nothing else holds the port, try another USB cable, and
confirm the board appears in Device Manager.

The app uses its own built-in copy of `avrdude`, so you do not need the
Arduino IDE installed for this.

---

## 10. Themes and settings file

Two colour schemes, from the **THEME** box in the toolbar:

- **Gold** — grey panels with a gold highlight *(default)*
- **Slate** — darker, with an orange highlight

Switching redraws the window. Your connection and any unsaved changes
survive, and a maximised window stays maximised.

The choice is remembered in a small file called `smartdriver.json`, written
next to the exe so the app stays portable. If that folder is read-only — a
USB stick, or Program Files — it goes to
`%APPDATA%\SmartDriverConfigurator\` instead.

The file is only created once you change something. Deleting it is
harmless; the app just starts on Gold again.

---

## 11. Keyboard shortcuts

| Key | Action |
|---|---|
| `Ctrl + K` | Connect / disconnect |
| `Ctrl + S` | Save the current page |
| `F5` | Reload the current page from the board |

---

## 12. Default values

What you get on a factory reset, or on a board that has never been
configured.

| Setting | Default |
|---|---|
| Receiver protocol | 3 × PWM |
| Channel count | 3 |
| Mixing | Transmitter |
| All reverse / swap flags | off |
| Min / Mid / Max (all channels) | 1000 / 1500 / 2000 µs |
| RC dead band | 8 µs |
| Driver dead counts | 40 |
| Start margin | 8 |
| Max speed | 255 |
| PWM frequency | 31 kHz |
| Failsafe timeout | 500 ms |
| Battery monitoring | **off** |
| Full voltage | 12600 mV |
| Cutoff voltage | 10500 mV |

To reset one page: **Default setting for tab**, then **Save**.

---

## 13. Troubleshooting

### The app will not connect

1. Is the COM port listed? If not, it is the USB driver —
   see [Step 2 of the main guide](../README.md#step-2--install-the-usb-driver).
2. Is something else holding the port? The Arduino IDE's Serial Monitor is
   the usual culprit.
3. Try disconnect and reconnect. The handshake window is only 2 seconds
   and a slow port open can miss it.
4. Try another USB cable — many cheap ones carry power only.

### Connected, but nothing moves

Check in this order:

| Check | If it fails |
|---|---|
| **Frames counter climbing?** | No → the receiver is not being heard. Check the signal wire, that the receiver is bound, and that it shares a ground with the Nano |
| **Armed showing 1?** | No → a failsafe is holding it. In 3-channel mode the most common reason by far is channel 3 not being received |
| **outL / outR moving?** | No → calibration. Mid is probably wrong, or the dead band is far too wide |
| All three fine, wheels still still | Wiring from the Nano to the drivers, or `R_EN`/`L_EN` not actually at 5 V, or no motor supply |

### Robot creeps with the sticks released

Redo the radio calibration ([section 5](#5-rc--calibration-page)). The
stored centre does not match where your radio actually rests. Raising the
dead band hides it, but calibration fixes it.

### Motor buzzes but will not turn

**Start margin** is too low. Raise it a few counts at a time.

### Robot lurches instead of starting gently

**Start margin** is too high. Lower it.

If you also changed the PWM frequency recently, check **Driver dead
counts** matches the new frequency — a stale value is the usual cause.

### Motors whine

The PWM frequency is set to 4 kHz or 500 Hz. Set it back to 31 kHz, **and
set Driver dead counts to 40** at the same time.

### Motors stutter under hard acceleration

The battery cutoff is tripping on voltage sag. It already waits a full
second, so if it still trips the pack genuinely is dropping below the
cutoff — a tired battery, wire that is too thin, or a cutoff set too high.

### Settings do not survive a power cycle

You changed them but did not **Save**. See
[section 4](#4-live-vs-saved--read-this-one). Changes go live immediately
but are only stored when you press Save on that page.

### A motor twitches when I connect

Check the status strip reads **v3**. This was a real bug in earlier
firmware and is fixed in v3 — reflash with the `.hex` in this repository.

### Everything is wrong and I want to start over

On each page: **Default setting for tab**, then **Save**. Then work
through [Step 5 of the main guide](../README.md#step-5--set-it-up) again,
in order.
