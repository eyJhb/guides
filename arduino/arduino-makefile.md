# Setting it up
- Project link - [https://github.com/sudar/Arduino-Makefile](https://github.com/sudar/Arduino-Makefile)

Install it using either the project link, or just `apt-get install arduino-mk`.
After that you will have `/usr/share/arduino`, which includes a couple of `.mk` files (`Arduino.mk`, `chipKIT.mk`, `Common.mk`, `Teensy.mk`).
Looking at the example found [here](https://github.com/sudar/Arduino-Makefile/blob/master/examples/Blink/Makefile), a basic `Makefile` is seen.

```
BOARD_TAG    = uno
include ../../Arduino.mk
```

Which can be written as 

```
BOARD_TAG = uno
include /usr/share/arduino/Arduino.mk
```

Then just have the `.ino` in the same folder, and run either `make` or `make upload`.
Remember to have the right permissions! (`usermod -a -G dialout $USER` and logout and login)
