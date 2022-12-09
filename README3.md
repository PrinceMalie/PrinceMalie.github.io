
# Wireguard Lab

## Digital Ocean Set-up 

1. Sign up for a [Digital Ocean Account](https://www.digitalocean.com/) 

2. Create a droplet
    * Choose any region and datacenter that you would like.

    * Choose the OS labeled Ubuntu and the version labeled 20.04 (LTS) x64.

    * Choose basic for the droplet type.

    * Choose the CPU optioned labeled Regular with SSD and click the payemnt plan labeled $4/mo. 

3. Choose an authentication method (SSH Key or Password). Which ever one you select, follow the instructions that appear once you click an option. 

## Docker & Docker-Compose Installation

This [source](https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/) was used to install docker. 

In addition, I launched a console window directly into the host from the Digital Ocean website. Follow the steps below to do the same:

* First, make sure you are on the page immediately after logging into your Digital Ocean account. 

* Click Droplets, which can be found on the sidebar of the webpage under the section labeled manage. 

* Click the name of the droplet you created.

* On the side bar, click the option labeled access.

* Lastly, click launch droplet console. 

Docker Installation: 

1. Install necessary tools:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```

2. Add docker key: 

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

3.  Add Docker repo ( this is 32bit/64bit OS. If you have a different operating system, click this [link](https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/), and find the code associated with your OS. )

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

4. Switch to correct repo

```
apt-cache policy docker-ce
```

5. Install Docker

```
sudo apt install docker-ce -y
```

6. If you are not root, run this to allow using Docker without sudo in the future:

```
sudo usermod -aG docker ${USER}
```

7. Log-out and log-in to use updated user-group setting.

8. Install Docker-Compose

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
9. Set permission

```
sudo chmod +x /usr/local/bin/docker-compose
```
## Wireguard Installation

This [source](https://thematrix.dev/setup-wireguard-vpn-server-with-docker/) was used to install wireguard. 

1. Run these on your server: 
```
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
```
2. Copy and paste the content below into docker-compose.yml:

```
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Hong_Kong
      - SERVERURL=1.2.3.4
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```
3. Make the changes listed below to finish editing docker-compose.yml:

    * TZ refers to timezone. Choose yours from TZ database name from [Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). (I selected America/Chicago.)

    * SERVERURL refers to the server IP address. Find it on the Digital Ocean dashboard.

    * PEERS are the number of user-config-files to generate, or the names of user-config-files. If you enter PEERS=3, it will generate peer_1, peer_2 and peer_3. If you enter PEERS=pc1,pc2,phone1, it will generate peer_pc1, peer_pc2 and peer_phone1.

4. After making the neccessary changes, save and exit the file by pressing CTRL + X, followed by Y. 

5. Start Wireguard by running these: 

```
cd ~/wireguard/
docker-compose up -d
```

## Wireguard Connection to Mobile Device and Laptop

### Mobile Device

1. Download Wireguard from the [iOS App Store](https://apps.apple.com/us/app/wireguard/id1441195209) or the [Google Play Store](https://play.google.com/store/apps/details?id=com.wireguard.android&hl=en_US&gl=US&pli=1)

2. Open Wireguard VPN application on your phone, click +, Create from QR code.

3. To receive the QR code:
```
docker-compose logs -f wireguard
```
4. Use the camera on your phone to scan the QR code.

5. Before turning on the VPN, go to [ipleak.net](https://ipleak.net/) and take a screenshot of the page. 

6. Turn on the VPN, and visit [ipleak.net](https://ipleak.net/). You should notice a change of location. Take a screenshot of this page as well.

7. Lastly, take a screenshot of the wireguard vpn page when the vpn is turned on. 

The screenshots associated with Wireguard for mobile device can be found below:

![IPLeak net (Mobile Device)](https://user-images.githubusercontent.com/73131545/206598768-27dee8ce-ef01-42ec-8fd3-5c8b5c0752ee.jpg)

![WireGuard VPN (Mobile Device)](https://user-images.githubusercontent.com/73131545/206598857-715f7f32-7a84-4aa9-8eb6-13a7fbac21a6.jpg)

![WireGuard IPLeak net (Mobile Device)](https://user-images.githubusercontent.com/73131545/206598938-ff365be5-b154-44a0-aee8-c8b9fd92f90a.jpg)

### Laptop

1. Download [Wireguard](https://www.wireguard.com/install/) for Windows or Mac. Follow the instructions that the installation wizard states. 

2. You must create the config file, so that it can imported to the Wireguard PC app. To do that folllow the steps listed below:
  
    * Run the code block below:

    ```
    cd ~/wireguard/config
    ```   

    * After the step above, you need to ls the current directory, so you can see what your username is (My username was named peer1)

    * Once you discover your username, run this code block:
    ```
    cd ~/wireguard/config/{username}
    ```

    * Run this code block (You will be copying the content of the conf file to the command line):
    ```
    cat {username}.conf
    ```

    * Lastly, copy the text and paste it in a text file. After pasting it in a text file, change the file extension from .txt to .conf. You can now import the conf to the Wireguard app. 

3. Before turning on the VPN, go to [ipleak.net](https://ipleak.net/) and take a screenshot of the page. 

4. Turn on the VPN, and visit [ipleak.net](https://ipleak.net/). You should notice a change of location. Take a screenshot of this page as well.

5. Lastly, take a screenshot of the wireguard vpn page when the vpn is turned on. 

The screenshots associated with Wireguard for laptop can be found below:

![IPLeak net (Laptop)](https://user-images.githubusercontent.com/73131545/206599087-777e33e6-01c6-4311-92d4-01efbbcb2af1.png)

![WireGuard (Laptop)](https://user-images.githubusercontent.com/73131545/206599190-6e4c98dd-b3c2-4d08-87e1-fb38a45c8856.png)

![WireGuard IPLeak net (Laptop)](https://user-images.githubusercontent.com/73131545/206599297-29d06b8f-3e0b-4840-9d08-55eba6ee8b4b.png)

