# :zap: lightning :zap:
The simple and lightweight network simulator based on Docker containers

![Preview](https://raw.githubusercontent.com/paaguti/lightning/master/screenshots/screenshot1.png)

![Preview](https://raw.githubusercontent.com/paaguti/lightning/master/screenshots/screenshot2.png)

## About lightning
Lightning is a simple and lightweight network simulator based on Docker containers.
Network scenarios are described in XML files that the program parses and interprets
for launching the networks and containers desired. Direct execution of the lightning functions is also
allowed.
Two Docker images are set by default, called "host" and "router":
*  **host** is a Debian based image with some programming and network management utilities inside
*  **router** is a Debian based OS with the Quagga routing suite installed

## Compatibility
Debian 9 x86_64 (compatibility with more OS will be checked in the nearly future)

## Before the installation
Before installing Lightning please check that your OS counts with the following **dependencies**:

* **docker-ce** (Docker Engine Community Edition)
A complete guide for installing Docker can be found in the official documentation of the project: https://docs.docker.com/engine/install/ (on the left panel select your distro and follow the instructions).

* **utilities**: brctl (command line tool for ethernet bridges manipulation), xmllint (XML parser), evince (PDF viewer), git
```bash
apt-get install bridge-utils libxml2-utils evince git mate-terminal
```
* **other utilities** that may probably be already installed in your OS:
```
apt-get install sudo bash x11-utils libc-bin coreutils iproute2 iptables mawk sed python3 python3-pip
```
* **Python dependencies** for the scenario verbaliser:
``` bash
python3 -m pip install --upgrade pip
python3 -m pip install lxml
```
** **Python** extra to get access to utilities installed with pip:
Add the following line to your `.bash_aliases` or `.zshenv` file if you haven't before

```bash
export PATH=$HOME/.local/bin:$PATH
```

## Install the program
* **Get the last version of the original project**
```bash
git clone https://github.com/ptoribi/lightning.git
```
* **or this fork for the scenario verbaliser and additional features (see below)**
```bash
git clone https://github.com/paaguti/lightning.git
```

* Change default locations **(Optional)** : You may change the location where the application folder and the symlink to the main program will be installed, by changing  the variables **LIGHTNING_INSTALLATION_DIRECTORY** and **SYMLINK_INSTALLATION_DIRECTORY** in the **lightning/install** file.

Please ensure before installing that both directories are included in your system's PATH variable. It is safe to keep the default values in most cases.

* **Install Lightning**
```bash
sudo lightning/install
```

## After the installation:
The user "root" should not execute Lightning directly, only regular users should. Regular users must execute Lightning with root privileges, this can be done by using **one** of these four different ways:

* **Adding the current user to the sudo group (warning!, that user will be allowed to execute all the programs in the system as root):**
```bash
sudo usermod -a -G sudo $(whoami)
```
* **Allowing that specific user to execute Lightning:**
```bash
sudo bash -c "echo 'USER_NAME ALL=(ALL) NOPASSWD: $(dirname $(readlink -f $(which lightning)))"/lightning"' >> /etc/sudoers"
```
* **Creating a new group and allowing all its members to execute Lightning, then adding the specific user to that group:**
```bash
sudo groupadd GROUP_NAME
sudo bash -c "echo '%GROUP_NAME ALL=NOPASSWD: $(dirname $(readlink -f $(which lightning)))"/lightning"' >> /etc/sudoers"
sudo usermod -a -G GROUP_NAME USER_NAME
```
* **Allowing all the users in the system to execute Lightning:**
```bash
sudo bash -c "echo 'ALL ALL=(ALL) NOPASSWD: $(dirname $(readlink -f $(which lightning)))"/lightning"' >> /etc/sudoers"
```

## Uninstall the program
```bash
sudo $(dirname $(readlink -f $(which lightning)))/uninstall
```

## How to use the program
The XML files describing the scenarios must be stored in the folder "scenarios" inside
the lightning installation folder, some default examples are provided. You can access it by executing:
```bash
cd $(dirname $(readlink -f $(which lightning)))/scenarios
```
To print a small usage message ans a list of the scenarios installed in the Virtual Laboratry, just type as a regular user in a shell:
```bash
lightning
```

To start a network scenario:
```bash
lightning start SCENARIO_NAME
```

To stop the network scenario that is being executed:
```bash
lightning stop
```

Finally, to cleanup any leftovers of a failed execution:
```bash
lightning purge
```
## Author
**Pablo Toribio** (under supervision of Dr. C.J. Bernardos Cano)

# EXTRAS (paaguti)

## Compatibilty

The latest version of lightning has been tested on Ubuntu 22.04LTS.

## Remote execution of lightning

In specific cases, the terminals to access the lightning devices need to be launched from outside the VM.

In this case, when executing `lightning start <scenario>`, the Docker commands to access the different devices
will be printed out to the console and to the file `$HOME/commands`.
You can then feed this file into a script that launches local terminals in the host, which docker through SSH
into the VM and access the different containers.
Additionally, lightning will create the file `$HOME/description.txt` with a textual description of the scenario
(in Spanish currently).

Check the value for `REMOTE` in `variables.conf`. If set to `0`, it will work inside the VM, launching the terminals and presenting the scenario wallpaper. If set to `1` lightning will start inside the VM, but expect the terminals
to access the containers to be executed in the host.

You can change the value of the `REMOTE` variable by launching `lightning` with the `-R` flag:

``` bash
lightning -R <value for REMOTE> start <scenario>
```

The value should be `0` or `1`  and will be kept until the next `lightning update`.

The default value for `REMOTE` is `0`

## sysctl customisation

Customisation for the sysctl.conf files inside the containers is provided by `/usr/local/lightning/sysctl-router.conf` for *router* containers and `/usr/local/lightning/sysctl-host.conf` for *host* containers. These files provide a way to define the *default*  values of specific variables. Manipulation is still handled by `lightning`.

## Support for the ARM64v8 architecture

Pulls the new multi-arch router and host images. This new version can be used on x86_64/amd64 and ARM aarch_64 architectures (like Apple silicon).

## Recover from locked router terminals.

On rare occasions, the router stysh is locked and the router configuration cannor be accessed. In this case, the script `lightning-reset-vty` will help unlocking the process.

Usage:
```bash
lightning-reset-vty <router name>
```
