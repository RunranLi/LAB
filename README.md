# LAB
Lab3
First we need to install the docker compose on ubuntu 
Install necessary tools:
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
Add Docker key:
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
Add Docker repo (choose the correct repository):
32bit / 64bit OS
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
Switch to correct repo:
apt-cache policy docker-ce
Install Docker:
sudo apt install docker-ce -y
If you are not root, run this to allow using Docker without sudo in the future:
sudo usermod -aG docker ${USER}
Install Docker-Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
Set permission:
sudo chmod +x /usr/local/bin/docker-compose

then we need to set up wireguard

Run these on your server:

mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
Copy and paste the content below:

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
There are several places you need to modify.

TZ refers to timezone. Choose yours from TZ database name from Wikipedia.
SERVERURL refers to the server IP address. Find it on Vultr or DigitalOcean dashboard.
PEERS are the number of user-config-files to generate, or the names of user-config-files. If you enter PEERS=3, it will generate peer_1, peer_2 and peer_3. If you enter PEERS=pc1,pc2,phone1, it will generate peer_pc1, peer_pc2 and peer_phone1.
Hit CTRL + X, Y, ENTER to save and exit the file.

Start Wireguard by running these:

cd ~/wireguard/
docker-compose up -d
It starts building the server. After you see Creating wireguard   ... done

Connect your phone to Wireguard
docker-compose logs -f wireguard
You will see the execution log, and QR codes of Wireguard VPN connection settings.

Open Wireguard VPN application on your phone, click +, Create from QR code
