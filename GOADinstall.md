# install virtualbox on vm
apt install fasttrack-archive-keyring
 add these 2 lines to /etc/apt/sources.list
	deb https://fasttrack.debian.net/debian-fasttrack/ bullseye-fasttrack main contrib
	deb https://fasttrack.debian.net/debian-fasttrack/ bullseye-backports-staging main contrib
apt update
apt install virtualbox
linux-headers-cloud-amd64
sudo dpkg-reconfigure virtualbox-dkms
sudo dpkg-reconfigure virtualbox

# install vagrant
%% [https://developer.hashicorp.com/vagrant/downloads](https://developer.hashicorp.com/vagrant/downloads) %% 
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vagrant
vagrant plugin install vagrant-vbguest

# git clone GOAD repo
apt install git
git clone [https://github.com/Orange-Cyberdefense/GOAD.git](https://github.com/Orange-Cyberdefense/GOAD.git)

# turn on vagrant
cd GOAD
vagrant up

# install docker
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] [https://download.docker.com/linux/debian](https://download.docker.com/linux/debian) "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# run ansible playbooks with docker

sudo docker build -t goadansible .

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory build.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory ad-servers.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory ad-trusts.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory ad-data.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory laps.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory servers.yml

###### TURN **OFF** NAT NETWORK ADAPTER ON DC02 BEFORE PROCEEDING ###

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory ad-relations.yml

###### TURN **ON** NAT NETWORK ADAPTER ON DC02 BEFORE PROCEEDING ###

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory adcs.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory ad-acl.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory security.yml

sudo docker run -ti --rm --network host -h goadansible -v $(pwd):/goad -w /goad/ansible goadansible ansible-playbook -i ../ad/sevenkingdoms.local/inventory vulnerabilities.yml