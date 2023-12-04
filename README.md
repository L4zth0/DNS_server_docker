# DNS_server_docker
This is repository to demostrate environment configuration to host adguard,  pihole amond others in raspberry Pi and docker

On the next image we can see the basic infrastructure of the environment

![image](https://github.com/L4zth0/DNS_server_docker/assets/15909248/41d0728b-4304-45ea-b55d-b959927a3542)

## 1. install docker
To begin, Docker needs to be installed on your system. Execute the following commands:
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

```
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 2. Install PiHole
Once Docker is installed, the next step is to set up Pi-hole. Use the following commands:
```
echo "### Make sure your IPs are correct, hard code ServerIP ENV VARs if necessary\nIP: ${IP}\nIPv6: ${IPv6}"
docker run -d \
    --name pihole \
    -p 53:53/tcp -p 53:53/udp \
    -p 67:67/udp \
    -p 80:80 \
    -p 443:443 \
    -v "${DOCKER_CONFIGS}/pihole/:/etc/pihole/" \
    -v "${DOCKER_CONFIGS}/dnsmasq.d/:/etc/dnsmasq.d/" \
    -e ServerIP="${IP}" \
    -e ServerIPv6="${IPv6}" \
    --restart=unless-stopped \
    --cap-add=NET_ADMIN \
    diginc/pi-hole:latest
```
## 3. Install AdGuard
Finally, install AdGuard using the following commands:
```
docker run --name adguardhome 
    --restart unless-stopped 
    -v adguard_data:/opt/adguardhome/work 
    -v adguard_config:/opt/adguardhome/conf 
    -p 53:53/tcp -p 53:53/udp 
    -p 80:80/tcp -p 443:443/tcp -p 443:443/udp -p 3000:3000/tcp 
    -d adguard/adguardhome
```
This guide helps you seamlessly set up a DNS server with Docker, incorporating Pi-hole and AdGuard for robust ad-blocking and DNS filtering capabilities. Ensure to verify and adjust IP configurations as needed for your environment.
