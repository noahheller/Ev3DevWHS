# Overview

This guide is for configuring the Lego Mindstorm to work after Lego's deprecation of the software.

To **cofigure** the Lego Mindstorms, you will need the following PER MINDSTORM

- A micoSD card (between 2GB and 32GB)
- USB-A wifi dongle

You will also need a **Wifi Router** but you can use one for all the Mindstorms.

## Flash EV3 Image

1. Download the [ev3dev-jessie image](https://github.com/ev3dev/ev3dev/releases/download/ev3dev-jessie-2017-09-14/ev3dev-jessie-ev3-generic-2017-09-14.zip)
3. Download and Install [Balena Etcher](https://etcher.balena.io/#download-etcher)
4. Open Balena Etcher and click the “Select image” button and browse to the folder where you downloaded an ev3dev release. Select the file that you downloaded. The release can be a `.img`.zip or `.img.xz`; whichever you have will work with Etcher.

![](https://www.ev3dev.org/images/etcher/image-selected.png)

5. Plug the SD card into your PC (if your PC doesn't have a micro SD slot, you can use an adapter or external reader). Etcher should detect the new device and display its information under the "Select drive" step. Confirm that the selected drive is correct.

![](https://www.ev3dev.org/images/etcher/drive-selected.png)

Note: If you have multiple removable drives available, you may need to use the "Change" button to select the proper device.

6. When you are confident that you have selected the correct drive, click "Flash!" and wait for the operation to complete.

7. If you arrive at this screen, you have successfully flashed your SD card.

![](https://www.ev3dev.org/images/etcher/success.png)

## Boot Mindstorm

Put the SD Card in your Mindstorm and power it on. At first, you will see the MINDSTORMS boot splash and the red LEDs will be on. This is immediately followed by the ev3dev boot splash and the LEDs changing to orange. The LEDs indicate disk (SD card) activity.

After about one minute, the screen will go blank. This happens on the first boot only. The first boot takes longer than subsequent boots because the mindstorm has to create a unique SSH host ids and take care of a few other housekeeping items. After another minute or two, you will see the brickman loading screen.

You will notice the number in the battery in the upper right corner. This displays the remaining voltage of the power supply. It is not possible to calculate an accurate percent value of the remaining energy, so this value is chosen. If the voltage drops below 5V the brick will turn off. All unsaved data will be lost. Keep in mind, that it may take a much longer time from 8V to 6.5V than from 6.5V down to 5V!

## Connect to a Mindstorm

Sadly, the Mindstorms can not connect to the school wifi. However, to program the robots we need an internet connect. To solve this you will need a wifi router. I suggest the following [cheap router](https://www.amazon.com/GL-iNet-GL-SFT1200-Secure-Travel-Router/dp/B09N72FMH5?dib=eyJ2IjoiMSJ9.WPP7ibE5vocZfCwdcpM3AZT35HOS-B6ACHUqWTN-5inEmEpLUJn92iSXexW3dz3yer6t5Hr8Ycc-PV0vM3qS1sQspTV8AE9QkHRF8k9f5Qvx7qOmCKUYfXi1ANjg4HoZK__lc-s4apslDLDFR1c6FicLFa4gjXRj5jDik_s5HGQzYNwJRk93zya6Nm5yG2bvxVX7XydPW_-mO8M4Aj1250rc3VSuaGgnvIwQg78OK250LoPZH910OBmS_DbUskxJQNbeHWejjJQMnzhTvp5oR4pmGZHUWd1_IxIa2JMiTWo.XwGJ4EwfMfR8IEHP6nxS2rb1Q8tb0WnanlbG2432c80).

### Setup wifi

Since you will need a router for devoloping with the Mindstorm, the simplest way to setup wifi is to use a Wi-Fi dongle connected through the Mindstorm's USB port. Note that the Wi-Fi dongle must support Linux out of the box otherwise you will have to install linux drivers by hand.

Once you have plugged in a dongle to the Mindstorm, select "Wireless and Networks > Wi-Fi" menu to connect to a network. Make sure you check the "Powered" box so that it starts searching for Wi-Fi networks, and then choose the one you want from the list that appears.

Next you want to connect you computer to the wifi network as well. (I hope you know how to do this already).

### SSH to mindstorm

If you are on a UNIX based operating system (Mac OSX or Linux) you can SSH to the mindstorm by running the command `ssh robot@ev3dev.local` If you have not changed the password, use the default password `maker` when prompted.

Congratuations! you are connected to the mindstorm!.

### Alternative SSH Method

If for what ever reason you need to SSH to the Mindstorm and you are unable to connect the mindstorm to the wifi router you can connect the Mindstorm to your computer's network but via USB.

Instructions on sharing an internet connection is very operating system specific so bare with me.

### Linux

1. Enable IP Forwarding by running the command `sysctl -w net.ipv4.ip_forward=1`
2. Create "dummy" virtual network interface `sudo ip link set name dummy_interface dev dummy0`. Replace all instances of **dummy_interface** with the name of the new adapter. (keep it simple you will have to reference this later)
3. Figure out what network interface is used for connecting to the internet. `route | grep '^default' | grep -o '[^ ]*$'` Replace all instances of **real_interface** with the output of this command
4. Forward traffic from **dummy_interface** to **real_interface**. `sudo iptables -A FORWARD -i dummy_interface -o real_interface -j ACCEPT`
5. Forward traffic from **real_interface** to **dummy_interface**
`sudo iptables -A FORWARD -i real_interface -o dummy_interface -j ACCEPT`
6. Enable network sharing on your **real_interface**. `sudo iptables -t nat -A POSTROUTING -o real_interface -j MASQUERADE`

Congratulations! That Mindstorm now was access to the same network as your computer.

### Windows and Mac OS

Follow this [tutorial](https://www.ev3dev.org/docs/tutorials/connecting-to-the-internet-via-usb/)

### Setup Block coding

                                                        e
Once you have SSHed to the mindstorm run the following commands

``` bash
sudo systemctl unmask openrobertalab
sudo systemctl start openrobertalab
```

This will enable a service that allows you to control the mindstorms via [roberta labs](http://lab.open-roberta.org/)

After running the commands above, it will start automatically after a reboot. You can turn it back off by running:

``` bash
sudo systemctl stop openrobertalab.service
sudo systemctl mask openrobertalab.service
```

If the openrobertalab package is installed and the service is running, the Open Roberta Lab menu item in brickman will allow you to connect to an Open Roberta server. This is how the menu will look like:

![](https://raw.githubusercontent.com/OpenRoberta/robertalab-ev3dev/refs/heads/develop/docs/MenuMain.png)

Once you selected the Open Roberta Lab menu item you'll get to this screen:

![](https://raw.githubusercontent.com/OpenRoberta/robertalab-ev3dev/develop/docs/RobertaLabDisconnected.png)

Click Connect. This will bring up a Pairing code that you have to type in from your computer

![](ttps://raw.githubusercontent.com/OpenRoberta/robertalab-ev3dev/refs/heads/develop/docs/RobertaLabConnecting.png)

#### Connecting to the Mindstorm via your computer

1. Go to <https://lab.open-roberta.org/>

2. Scroll down till you see A navigation bar that says "Popular Systems" and "All systems and filter options". Click the "All systems and filter options".

3. This will then open a search bar where you will type "ev3dev".

3. Click on "get started"

4. In the top right Click on the "robot" drop down and selected connect.

5. Type in the parring code that is displayed on your mindstorm.

6. Click on the tab "ROBOT CONFIGURATION" and move the sensor and motors into the correct slots.

That is pretty much it. You are ready to program your mindstorm. When you are done coding you can press the play button.
