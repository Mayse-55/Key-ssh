# Configuration SSH pour Guacamole

## Génération de la clé SSH

```bash
sudo ssh-keygen -t ed25519 -f /etc/guacamole/guac_key -N ""
```

## Copie de la clé vers le serveur cible

```bash
ssh-copy-id -i /etc/guacamole/guac_key.pub root@192.168.1.12
```

## Test de connexion

```bash
ssh -i /etc/guacamole/guac_key root@192.168.1.12
```

## Configuration SSH sur Debian 12

Éditer le fichier de configuration :

```bash
sudo nano /etc/ssh/sshd_config
```

Vérifier ces paramètres :

```
PermitRootLogin prohibit-password
PubkeyAuthentication yes
PasswordAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

Redémarrer SSH :

```bash
sudo systemctl restart sshd
```
