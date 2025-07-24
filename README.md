# Configuration SSH (connexion sans mot de passe via clé SSH)

---

## 1. Génération de la clé SSH sur le serveur

Pour que Guacamole puisse se connecter à tes serveurs Debian sans mot de passe, il faut générer une paire de clés SSH **sans passphrase** (c’est-à-dire sans mot de passe sur la clé).

Exécute cette commande sur le serveur :

```bash
sudo ssh-keygen -t ed25519 -f /etc/guacamole/guac_key -N ""
```

* `-t ed25519` : type de clé moderne, sécurisée et rapide.
* `-f /etc/guacamole/guac_key` : emplacement où sera stockée la clé privée.
* `-N ""` : définit une passphrase vide, donc la clé ne demandera pas de mot de passe lors de l’utilisation.

---

## 2. Copier la clé publique sur le serveur Debian cible

Pour permettre au serveur de se connecter **sans mot de passe**, la clé publique générée doit être ajoutée dans le fichier `authorized_keys` de l’utilisateur sur la machine cible (par exemple `root`).

Utilise cette commande depuis le serveur Guacamole :

```bash
ssh-copy-id -i /etc/guacamole/guac_key.pub root@192.168.1.12
```

* Cette commande ajoute automatiquement le contenu de la clé publique dans `/root/.ssh/authorized_keys` sur la machine Debian distante.
* Remplace `root` par l’utilisateur cible si tu ne souhaites pas utiliser `root`.

---

## 3. Tester la connexion SSH manuellement

Avant de configurer le serveur, il est essentiel de vérifier que la connexion SSH fonctionne correctement et sans mot de passe.

Sur le serveur, lance :

```bash
ssh -i /etc/guacamole/guac_key root@192.168.1.12
```

* Si tu arrives directement dans la session sans mot de passe, la configuration est bonne.
* Sinon, il faudra vérifier les permissions et la configuration SSH.

---

## 4. Configuration SSH sur la machine Debian 12 (serveur cible)

Pour autoriser l’authentification par clé SSH et définir les restrictions d’accès root, modifie la configuration SSH.

Édite le fichier :

```bash
sudo nano /etc/ssh/sshd_config
```

Assure-toi que ces paramètres sont présents et correctement configurés (optionnel) :

```
PermitRootLogin prohibit-password
PubkeyAuthentication yes
PasswordAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

* `PermitRootLogin prohibit-password` : autorise la connexion root **uniquement par clé SSH**, pas par mot de passe.
* `PubkeyAuthentication yes` : active l’authentification par clé publique.
* `PasswordAuthentication yes` : autorise encore la connexion par mot de passe (utile pendant les tests, tu pourras désactiver plus tard).
* `AuthorizedKeysFile .ssh/authorized_keys` : chemin du fichier contenant les clés autorisées.

---

## 5. Redémarrer le service SSH pour appliquer les changements

Applique la nouvelle configuration en redémarrant le service SSH :

```bash
sudo systemctl restart sshd
```

---

## Résumé rapide

| Étape                          | Commande / Action                                              |
| ------------------------------ | -------------------------------------------------------------- |
| Générer clé SSH                | `sudo ssh-keygen -t ed25519 -f /etc/guacamole/guac_key -N ""`  |
| Copier clé publique vers cible | `ssh-copy-id -i /etc/guacamole/guac_key.pub root@192.168.1.12` |
| Tester la connexion            | `ssh -i /etc/guacamole/guac_key root@192.168.1.12`             |
| Modifier sshd\_config          | Modifier `PermitRootLogin`, `PubkeyAuthentication`, etc.       |
| Redémarrer SSH                 | `sudo systemctl restart sshd`                                  |

---
