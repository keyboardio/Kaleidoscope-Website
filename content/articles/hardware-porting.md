---
date: "2019-04-16T08:33:45+02:00"
title: "Porting to a new keyboard"
type: post
tags: [guide, programming]
---

The goal of this guide is to show you how to add support for a new keyboard to
[Kaleidoscope][k]. It doesn't go further than that: this guide doesn't help you
maintain an hardware plugin in an external repository, nor does it document
everything that makes for a good pull request for a keyboard. It goes far enough
to have it working, in a quick and dirty way, anything further than that will be
the topic of another guide.

 [k]: https://github.com/keyboardio/Kaleidoscope

To make things simple, this guide is focused on a narrower set of keyboards:
those powered by an `ATMega32U4` controller, which do not use I2C or anything
similar.

## Architectural overview

## The board we'll build a plugin for

TODO (4x5 numpad)

## Creating the hardware plugin

### `src/kaleidoscope/hardware/example/NumPad.h`

#### Binding the header to the hardware

{{< code numbered="true" >}}
[[[#pragma once]]]

#ifdef [[[ARDUINO_AVR_NUMPAD]]]
{{< /code >}}

1. TODO
2. TODO

#### Setting the stage

{{< code numbered="true" >}}
#include <Arduino.h>
#define [[[HARDWARE_IMPLEMENTATION]]]           \
  [[[kaleidoscope::hardware::example::NumPad]]]
#include "Kaleidoscope-HIDAdaptor-KeyboardioHID.h"

#include "kaleidoscope/macro_helpers.h"
#include [[["kaleidoscope/hardware/avr/pins_and_ports.h"]]]

#include [[["kaleidoscope/hardware/ATMegaKeyboard.h"]]]
#include [[["kaleidoscope/driver/bootloader/avr/Caterina.h"]]]
{{< /code >}}

 1. Some parts of the firmware need to know the class that implements the hardware it is being compiled for. To make this simple, we set the `HARDWARE_IMPLEMENTATION` macro to the class name of our hardware plugin.
 2. As explained before, hardware plugins live in a vendor namespace (`example` in our case) under `kaleidoscope::hardware`, and the class is named after the board (`NumPad`).
 3. The `avr/pins_and_ports.h` header contains definitions for the PIN and Port macros common to all AVR devices. We'll be using these (the pins) to set up the key matrix for our keyboard.
 4. The `ATMegaKeyboard.h` header implements a helper base-class, which abstracts away most of the boring parts of hardware support, at least for keyboards based on the `ATMega32U4` MCU.
 5. We also include a header for the bootloader used on the MCU. For this example, we'll be using Caterina, but `HalfKay` (Teensy) and `FLIP` are also supported. The bootloader is used to allow restarting the keyboard in programmable mode, from within the firmware.

#### The hardware class itself

{{< code numbered="true" >}}
namespace kaleidoscope {
namespace hardware {
namespace example {
class [[[NumPad]]]: public [[[ATMegaKeyboard<NumPad>]]] {
  [[[friend class ATMegaKeyboard<NumPad>]]];
 public:
  NumPad(void) {
    [[[mcu_.disableJTAG()]]];
  }

  [[[ATMEGA_KEYBOARD_CONFIG]]](
    [[[ROW_PIN_LIST]]]({ PIN_D0, PIN_D1, PIN_D2, PIN_D3 }),
    [[[COL_PIN_LIST]]]({ PIN_F0, PIN_F1, PIN_F4, PIN_F5, PIN_F6 })
  );

  static constexpr int8_t [[[led_count = 0]]];

 protected:
  driver::bootloader::avr::Caterina [[[bootloader_]]];
};

}
}
}
{{< /code >}}

1. TODO
2. TODO
3. TODO
4. TODO
5. TODO
6. TODO
7. TODO
8. TODO
9. TODO

#### Keymaps and other helpers

{{< code numbered="true" >}}
#define [[[KEYMAP]]](            \
    r0c0, r0c1, r0c2, r0c3,   \
    r1c0, r1c1, r1c2, r1c3,   \
    r2c0, r2c1, r2c2, r2c3,   \
    r3c0, r3c1, r3c2, r3c3,   \
    r4c0, r4c1, r4c2, r4c3    \
)                             \
{                             \
  { r0c0, r0c1, r0c2, r0c3 }, \
  { r1c0, r1c1, r1c2, r1c3 }, \
  { r2c0, r2c1, r2c2, r2c3 }, \
  { r3c0, r3c1, r3c2, r3c3 }, \
  { r4c0, r4c1, r4c2, r4c3 }, \
}

#include "[[[kaleidoscope/hardware/key_indexes.h]]]"

[[[extern kaleidoscope::hardware::example::NumPad &NumPad;]]]
{{< /code >}}

1. TODO
2. TODO
3. TODO

#### Wrapping up

{{< code numbered="true" >}}
[[[#endif]]]
{{< /code >}}

1. TODO

## Making the board available to Arduino

TODO
