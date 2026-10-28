# kafka-ansible

Déploiement de Kafka (mode KRaft) dans Docker avec Ansible. L'image est construite
localement à partir d'un Dockerfile généré par un template Jinja2 (pas d'image `apache/kafka`).

## Pré-requis

- Docker, Python, Ansible, Docker SDK for Python, Git
- au moins 4 Go de mémoire disponible
- à exécuter sous Linux / WSL (Ansible ne tourne pas nativement sous Windows)

## Installation

```bash
pip install ansible docker
ansible-galaxy collection install -r requirements.yml
```

## Build et déploiement

```bash
ansible-playbook -i inventories/dev/hosts.yml playbooks/deploy.yml
```

Tags disponibles : `network`, `volume`, `image`, `build`, `config`, `container`, `verify`.

```bash
ansible-playbook -i inventories/dev/hosts.yml playbooks/deploy.yml --tags config
```

Le Dockerfile et la configuration générés se trouvent dans `build/`.

## Vérification

```bash
ansible-playbook -i inventories/dev/hosts.yml playbooks/verify.yml
```

Contrôles : conteneur démarré, port disponible, création d'un topic, production et consommation d'un message.

Idempotence (la 2e exécution ne doit rien changer côté déploiement) :

```bash
ansible-playbook -i inventories/dev/hosts.yml playbooks/deploy.yml
ansible-playbook -i inventories/dev/hosts.yml playbooks/deploy.yml --check --diff
```

## Destruction

```bash
# supprime le conteneur et le réseau (les données sont conservées)
ansible-playbook -i inventories/dev/hosts.yml playbooks/destroy.yml

# supprime aussi le volume : PERTE DES DONNÉES KAFKA
ansible-playbook -i inventories/dev/hosts.yml playbooks/destroy.yml -e kafka_destroy_volume=true
```

## Choix techniques

- **Variables** : valeurs par défaut dans `roles/kafka/defaults/main.yml`, valeurs de
  l'environnement dans `group_vars/all.yml` (chargé via le lien `inventories/dev/group_vars`).
- **Dockerfile templatisé** : l'image de base, la version de Kafka, les ports et les chemins
  sont paramétrables ; la version de Kafka et le tag d'image sont fixés (pas de `latest`).
- **Utilisateur non privilégié** : Kafka tourne sous l'utilisateur `kafka`, pas `root`.
- **KRaft** : pas de ZooKeeper ; le stockage est formaté au démarrage avec
  `--ignore-formatted`, ce qui rend le redémarrage sans risque.
- **Volume Docker** : les données (`log.dirs`) survivent à la recréation du conteneur.
- **Handler** : le conteneur n'est redémarré que si la configuration ou le Dockerfile change ;
  l'image n'est reconstruite que dans ce cas (`force_source`).
- **Configuration montée** : `server.properties` est aussi monté dans le conteneur, ce qui
  permet d'appliquer une configuration par broker.

## Limites

- un seul broker, sans sécurité (PLAINTEXT, pas de TLS ni d'authentification) ;
- `advertised.listeners` sur `localhost` : clients uniquement depuis la machine hôte ;
- `--check` ne peut pas vérifier la vérification fonctionnelle (elle est ignorée dans ce mode).
