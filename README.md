# automation-of-kafka
Déploiement de Kafka avec Ansible, Dockerfile et templates Jinja2

# 1. Tout installer
sudo apt update
sudo apt install -y docker.io ansible python3-docker

# 2. Autoriser ton utilisateur à utiliser Docker
sudo usermod -aG docker $USER
newgrp docker                  # applique le droit dans ce terminal, sans se déconnecter
docker run --rm hello-world    # doit afficher "Hello from Docker!"

# 3. Le projet
cd ~
tar xzf kafka-ansible-final.tar.gz
cd kafka-ansible
ansible-galaxy collection install -r requirements.yml

# 4. Lancer
ansible-playbook -i inventories/dev/hosts.yml playbooks/deploy.yml
