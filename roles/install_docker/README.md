
## Ansible-Роль установка Docker

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

sudo curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg

sudo echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/trusted.gpg.d/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo find / -name "docker-compose"
/usr/libexec/docker/cli-plugins/docker-compose 
sudo ln -s /usr/libexec/docker/cli-plugins/docker-compose /bin/docker-compose
docker-compose --version
apt upgrade python*

