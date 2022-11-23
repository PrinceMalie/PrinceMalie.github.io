# Docker Project

## Install Docker 

1. Docker will be installed by downloading it directly from the docker repository. Please run the rullowing code block to install Docker: 

```
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker sysadmin
```
2. To ensure docker installed sucessfully, please run the command listed below. You should receive output that says the following, "Hello from Docker! This message shows that your installation appears to be working correctly ..."

```
sudo docker run hello-world
```

## Install Docker Compose

1. Use the code block below to install docker compose:

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```
2. To ensure that docker compose installed correctly, run the command down below. You should receive an output that states the version number and the build number of docker compose: 

```
docker-compose --version
```
## Install OpenVAS

1. Run the code block listed down below to install OpenVas: 

```
sudo docker run -d -p 443:443 --name openvas mikesplain/openvas
```
2. Wait 10-20 minutes for OpenVAS to install all the new NVT's and vulnerability signatures.

3. Type the ip-address in your web browser, and you will be able to access OpenVas ("admin" is the default username and password for OpenVas).


Here is the image of the final product (I could not get tasks to work, but OpenVas works!):

![OpenVas Works](https://user-images.githubusercontent.com/73131545/203458566-8a2b532b-cee3-4c2f-b694-5cd88fa5dc91.png)







