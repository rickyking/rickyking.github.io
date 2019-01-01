---
title: "Raspberry's Sense HAT: seen in space(ISS)"
date: 2015-09-01 23:13:14
tags: [raspi, sensor]
categories: [Raspberry Pi]
---
{% asset_img IMG_0043.JPG %}

I recently purchased a new add-on for Raspberry. With a successful advertisement of sending the Sense HAT to Internation Space Station, now the widget is ready for purchase: [link](https://www.raspberrypi.org/blog/buy-the-sense-hat-as-seen-in-space/), [online shopping](http://swag.raspberrypi.org/products/raspberry-pi-sense-hat), [french store](kubii.fr).

To begin with the little board, all you have to do is to run the following command in order to install the driver:

```bash
sudo apt-get update
sudo apt-get install sense-hat
sudo pip-3.2 install pillow # used for python 3.2
```

A reboot of the raspberry will give you access to the board.

## Troubleshooting

**Cannot get sense-hat installed**

This problem seems to be related to the operating system. I have `HypriotOS` installed, and the os repository does not provide `sense-hat`, so I reinstall the original image of `raspbian`.

**Still not working**

Make sure you have reboot your raspberry. Once the Sense HAT shows no LED after reboot, this means it is sucessfully installed.

Make sure you have enabled I2C by `sudo raspi-config`, select `8. Advanced Options - I2C - Enable`

## Heart Program

With provided [example](https://github.com/RPi-Distro/python-sense-hat/blob/master/examples/README.md), you can already play with the sense HAT.

Here is a quick start with `Hello world`.

```python
from sense_hat import SenseHat # import sense_hat object
sense = SenseHat()
sense.show_message("Hello world!") # show hello world
```

So I make a deeper step, by showing a heart in order to convience my GF (lol) for the purchase:

```python
from sense_hat import SenseHat

X = (255, 0, 0)
O = (0, 0, 0)

heart = [
    O, O, O, O, O, O, O, O,
    O, O, X, O, O, X, O, O,
    O, X, X, X, X, X, X, O,
    O, X, X, X, X, X, X, O,
    O, X, X, X, X, X, X, O,
    O, O, X, X, X, X, O, O,
    O, O, O, X, X, O, O, O,
    O, O, O, O, O, O, O, O
]

sense = SenseHat()
sense.set_pixels(heart)
```
