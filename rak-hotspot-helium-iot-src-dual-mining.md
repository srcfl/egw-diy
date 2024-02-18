# Getting started with the Srcful Energy Gateway

This guide will help you get started with the Srcful Energy Gateway. Depending on your hardware, you can choose between two different approaches to get started. The first approach is for users who have a `Rak Hotspot Miner V2` and want to dual-mine `IoT` and `SRC`. The second approach is for users who have followed the [Build your own Srcful Energy Gateway](diy.md) guide and want to mine `SRC` only

This guide is for installing the dual mining firmware on a `Rak Hotspot Miner V2`. It is recommended that you first run the miner with the normal firmware and complete the installation process including checking that it is active in the Helium network before you proceed.

## Step 1: Preparing the Raspberry Pi

### Select a Lightweight Linux Distro

- Recommended: Raspberry Pi OS (64-bit).

### Download and Use the Raspberry Pi Imager

- Download the Raspberry Pi Imager from the [official Raspberry Pi website](https://www.raspberrypi.org/software/).
- Use it to burn Raspberry Pi OS Lite (64-bit) to an SD card.

### Image Configuration
Use the Raspberry Pi Imager to:
 - enable SSH
 - enable WiFi

### Insert the SD Card and Power On

- Insert the prepared SD card into the Raspberry Pi and power it on.

### Connect to Your Raspberry Pi

- Use SSH to connect remotely (`ssh pi@<RASPBERRY_PI_IP>`).

## Step 2: Setting Up the Raspberry Pi

### Configure and Update Your Raspberry Pi
To set up locale, and timezone, run:
```shell
sudo raspi-config
```

Time to update Your Raspberry Pi, run:

```shell
sudo apt-get update && sudo apt-get upgrade -y
```

Restart your Raspberry Pi after the update by running `sudo reboot` and then log in again using ssh.

### Enable i2c and SPI for Secure Element

Note The SPI parts are only needed if you run `Rak Hotspot Miner V2` as it has a radio module that is needed for communication.

```shell
sudo raspi-config
```

- Select `Interfacing Options`.
- Select `I2C`.
- Select `Yes` to enable the ARM I2C interface.
- Select `SPI`
- Select `Yes` to enable the SPI interface.
- Select `Finish` to exit the configuration tool.

Next, if you are on a `Rak Hotspot Miner V2`, we need to configure the SPI device to use only one channel chip select (cs). We do this by editing the boot configuration Run:

```shell
sudo nano /boot/firmware/config.txt
```

and add the following line to the file 

```shell
dtoverlay=spi0-1cs
```

Press `ctrl+s` to save and then `ctrl+x` to exit.

Restart your Raspberry Pi after the update by running:
```shell
sudo reboot
```

then log in again using ssh.

### Install Docker

Now install Docker on your Raspberry Pi and add your user to the `docker` group by running the following commands:

```shell
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Now install the latest version of Docker and docker-compose:

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Add your user to the `docker` group:

```shell
sudo usermod -aG docker $USER
```

Now log out and log back in to apply the changes. To log out, run:
```shell
exit
```

then log back in using SSH.

Confirm that Docker is running by running: 

```shell
docker run hello-world
```

The output should be similar to the following:



> Hello from Docker!  
> This message shows that your installation appears to be working correctly.

### Clone the GitHub Repository

First, you need to clone the repository containing the Docker Compose file. Use the following commands to clone the repository and navigate to the directory:

```shell
git clone https://github.com/srcfl/srcful-gateway.git
```

## Step 3: Running the firmware using Docker Compose



You are soon there, now we are ready to start the gateway!  

NOTE: These steps should be repeated **every time** you want to start the gateway.

First you need to be in the correct directory and make sure you have the latest version of the firmware:

```shell
cd srcful-gateway
git pull
```

### Set the device IP
The firmware needs the device local ip set to the environment variable `HOST_IP`:

```shell
export HOST_IP=$(ip -4 addr show scope global dev $(ip route|awk '/default/ { print $5 }') | grep inet | awk '{print $2}' | cut -d / -f 1)
```

It is likely a good idea to use a static IP for both your inverter and your eGW. Refer to your router manufacturer on how to set this up.

### Run Docker Compose

The repository contains two different Docker Compose files. The first one is for running the firmware on a `Rak Hotspot Miner V2` and the second one is for running the firmware on a `Raspberry Pi` with a secure element. Regardless of which command you will run, it might take a few minutes to download the necessary Docker images the first time you run it.

#### Running the firmware on a `Rak Hotspot Miner V2`

If you have a `Rak Hotspot Miner V2`, run the following command:

```shell
docker compose -f compose-rak.yml up
```

To run the firmware in the background (detached), add the `-d` flag:

```shell
docker compose -f compose-rak.yml up -d
```

You can then check the logs for any errors:
```shell
docker logs
```

## Step 4: Post-Setup Configuration

After running the firmware, you need to configure the gateway by setting WiFi credentials, a wallet address, and inverter settings. You can do this by using the [srcful-configurator](https://configurator.srcful.io/) tool. Not that the configurator currently only work on Android, Windows, MacOS and linux. The configurator does not work on iPhones.

If you are on a `Rak Hotspot Miner V2` bluetooth connection will be available for 3 minutes after services are started or after double clicking the gateway button. The helium bluetooth is activated by long pressing the same button.

