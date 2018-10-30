# Installing arduino IDE
First go to the [download](https://www.arduino.cc/en/Main/Software) page, and get the newest version.
Extract so it is in your `~/.local/share/arduino`, then you can add it to your path and run the `install.sh` located in `~/.local/share/arduino`.

```
cd ~/.local/share/arduino
./install.sh
echo 'PATH="$PATH:$HOME/.local/share/arduino"' >> ~/.profile
```
