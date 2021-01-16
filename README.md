# MinecraftSystemdUnit
Systemd Unit file for Minecraft Server

## Installation

1. Install the necessary packages 
    ```bash
    apt-get install -y default-jre-headless screen curl
    ```
2. Now you need to create the user for the service
    ```bash
    adduser --system --shell /bin/bash --home /opt/minecraft --group minecraft
    chmod +t /opt/minecraft
    ```
1. Download the Systemd Unit file:
    ```bash
    curl https://raw.githubusercontent.com/Seneliux/MinecraftSystemdUnit/master/mc%40.service > /etc/systemd/system/mc@.service
    ```

## Setup Instance

Each server has it's own subdirectory of `/opt/minecraft`. This means that when a new server is being added, the following steps need to be followed:

1. Create a subdirectory of `/opt/minecraft` for your Minecraft server, we'll be using `papermc` as an example
    - It is recommended to use a name which only includes lowercase characters, spaces should be replaced with dashes
    ```bash
    cd /opt/minecraft
    mkdir paper
    ```
2. Accept the Minecraft server's EULA
    ```bash
    cd paper
    echo 'eula=true' > eula.txt

3. Upload your files to the `/opt/minecraft/paper` directory from [this PaperMC page](https://papermc.io/downloads) by copying the address of the latest build and download (today is #416, 1.16.4 version):
```bash
curl https://papermc.io/api/v2/projects/paper/versions/1.16.5/builds/418/downloads/paper-1.16.5-418.jar > /opt/minecraft/paper/paper-1.16.5.jar
 ```
- **Note**: All uploaded files should be owned by the `minecraft` user

```bash
    chown -R minecraft:minecraft /opt/minecraft
```

### RAM allocation

With `mc@.service` it's possible to specify the RAM allocation per server. This can be done using a `server.conf` file in the server's directory.

**Example**

```properties
# /opt/minecraft/papermc/server.conf
MCMINMEM=512M
MCMAXMEM=2048M
```
### CPU cores

Set the number of used CPU cores:

```properties
PARALLEL_THREADS=2
```

## Usage

### Enable auto-start Minecraft server on boot

```bash
systemctl enable mc@paper
```

### Disable auto-start Minecraft server on boot

```bash
systemctl disable mc@paper
```

### Start Minecraft server manually

```bash
systemctl start mc@paper
```

### Stop Minecraft server manually

```bash
systemctl stop mc@paper
```

### Connecting to the Minecraft server console

To enter the console, [`screen`](https://linux.die.net/man/1/screen) is used.

All screen sessions are owned by the `minecraft` user and are prefixed with `mc-`.

```bash
# This will attach to the Minecraft server called `ftb-beyond`
su -c 'screen -r mc-paper' minecraft
```

**Note**: To detach (exit) from the session, press <kbd>CTRL + A</kbd> followed by <kbd>D</kbd>.

## Guest from an outside world
To connect from the outside world, must open the 25565/tcp port. UFW case:
```bashapplications.d/
curl https://raw.githubusercontent.com/Seneliux/MinecraftSystemdUnit/master/ufw_application > /etc/ufw/applications.d/minecraft
ufw allow minecraft
```
If you add another minecraft instance, additionaly add not used ports.
```bash
nano /etc/ufw/applications.d/minecraft
```
Change line 
```properties
ports=25565/tcp
```
to 
```properties
ports=25565,25566,25567/tcp
```
This will open three ports for three different instances. 
Accordinaly change port in the file `server.conf`in every instance:
```properties
server-port=25565
```
