# Getting started with the Srcful Energy Gateway

This guide will help you get started with the Srcful Energy Gateway. Depending on your hardware, you can choose between two different approaches to get started. The first approach is for users who have a `Rak Hotspot Miner V2` and want to dual-mine `IoT` and `SRC`. The second approach is for users who have followed the [Build your own Srcful Energy Gateway](diy.md) guide and want to mine `SRC` only

## Step 1: Preparing the Raspberry Pi

### Select a Lightweight Linux Distro

- Recommended: Raspberry Pi OS Lite (64-bit) for its lightweight nature and compatibility.

### Download and Use the Raspberry Pi Imager

- Download the Raspberry Pi Imager from the [official Raspberry Pi website](https://www.raspberrypi.org/software/).
- Use it to burn Raspberry Pi OS Lite (64-bit) to an SD card.

### Enable SSH

- Use the Raspberry Pi Imager to enable SSH and Wi-Fi by.

### Insert the SD Card and Power On

- Insert the prepared SD card into the Raspberry Pi and power it on.

### Connect to Your Raspberry Pi

- Use SSH to connect remotely (`ssh pi@<RASPBERRY_PI_IP>`).

## Step 2: Setting Up the Raspberry Pi

### Configure Your Raspberry Pi

- Run `sudo raspi-config` to set up locale, and timezone.

### Update Your Raspberry Pi

```shell
sudo apt-get update && sudo apt-get upgrade -y
```

Restart your Raspberry Pi after the update by running `sudo reboot`.

### Enable i2c for Secure Element

```shell
sudo raspi-config
```

- Select `Interfacing Options`.
- Select `I2C`.
- Select `Yes` to enable the ARM I2C interface.
- Select `Finish` to exit the configuration tool.

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

Now log out and log back in to apply the changes. To log out, run `exit` and then log back in using SSH.

Confirm that Docker is running by running `docker run hello-world`. The output should be similar to the following:

```shell
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

## Step 3: Running the firmware using Docker Compose

There are two different docker-compose files for running the firmware. The first one is for running the firmware on a `Rak Hotspot Miner V2` and the second one is for running the firmware on a `Raspberry Pi` with a secure element.

### Clone the GitHub Repository

First, you need to clone the repository containing the Docker Compose file. Use the following commands to clone the repository and navigate to the directory:

```shell
git clone git@github.com:srcfl/srcful-gateway.git
cd srcful-gateway
```

### Set the device IP
The firmware needs the device local ip set to the environment variable `HOST_IP`.

```shell
export HOST_IP=$(ip -4 addr show scope global dev $(ip route|awk '/default/ { print $5 }') | grep inet | awk '{print $2}' | cut -d / -f 1)
```

It is likely a good idea to use a static IP for both your inverter and your eGW. Refer to your router manufacturer on how to set this up.

### Run Docker Compose

The repository contains two different Docker Compose files. The first one is for running the firmware on a `Rak Hotspot Miner V2` and the second one is for running the firmware on a `Raspberry Pi` with a secure element. Regardless of which command you will run, it might take a few minutes to download the necessary Docker images the first time you run it.

#### Running the firmware on a `Rak Hotspot Miner V2`

If you have a `Rak Hotspot Miner V2`, run the following command:

```shell
docker compose -f docker-compose-dual-mining.yml up
```

To run the firmware in the background, add the `-d` flag:

```shell
docker compose -f docker-compose-dual-mining.yml up -d
```

#### Running the firmware on a `Raspberry Pi` with a secure element

If you have a `Raspberry Pi` with a secure element, run the following command:

```shell
docker compose -f docker-compose.yml up
```

To run the firmware in the background, add the `-d` flag:

```shell
docker compose -f docker-compose.yml up -d
```

## Step 4: Post-Setup Configuration

- After running the firmware, you need to configure the gateway by setting WiFi credentials, a wallet address, and inverter settings. You can do this by using the [srcful-configurator](https://configurator.srcful.io/) tool. Not that the configurator currently only work on Android, Windows, MacOS and linux. The configurator does not work on iPhones.
