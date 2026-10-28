# Rôle kafka

Construit une image Kafka à partir d'un Dockerfile templatisé, puis déploie le conteneur.

## Tâches

1. crée le réseau et le volume Docker ;
2. crée le contexte de build et génère `server.properties`, `kafka.env` et le `Dockerfile` ;
3. construit l'image `{{ kafka_image_name }}:{{ kafka_image_tag }}` ;
4. crée et démarre le conteneur (port publié, volume monté, politique de redémarrage).

## Handler

`Restart Kafka container` : déclenché quand la configuration ou le Dockerfile change.

## Variables principales

| Variable | Défaut |
|---|---|
| `kafka_version` | `3.9.0` |
| `kafka_base_image` | `eclipse-temurin:21-jre-jammy` |
| `kafka_image_name` / `kafka_image_tag` | `local/kafka` / `3.9.0-training` |
| `kafka_container_name` | `kafka-1` |
| `kafka_host_port` / `kafka_container_port` / `kafka_controller_port` | `9092` / `9092` / `9093` |
| `kafka_data_dir` | `/var/lib/kafka/data` |
| `kafka_restart_policy` | `unless-stopped` |

La liste complète se trouve dans `defaults/main.yml`.
