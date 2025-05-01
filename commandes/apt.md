# 🐧 Pense-bête `apt` – Debian / Ubuntu

Un mémo rapide et clair pour utiliser `apt` et `apt-get`, le gestionnaire de paquets 🧠

---

## 🛠️ Commandes principales

| Commande                          | Description                                 | Exemple                                |
|----------------------------------|---------------------------------------------|----------------------------------------|
| `sudo apt install`               | 🔽 Installer un paquet                      | `sudo apt install firefox`             |
| `sudo apt remove`                | 🗑️ Supprimer un paquet                     | `sudo apt remove firefox`              |
| `apt list --installed`          | 📋 Lister les paquets installés            | `apt list --installed`                 |
| `apt search`                     | 🔍 Rechercher un paquet                    | `apt search vlc`                       |
| `apt show`                       | 📄 Infos détaillées d’un paquet            | `apt show vlc`                         |
| `sudo apt update`                | 🔄 Met à jour la base des paquets          | `sudo apt update`                      |
| `sudo apt upgrade`              | 🆙 Met à jour les paquets installés       | `sudo apt upgrade`                     |
| `sudo apt full-upgrade`         | 🆙 Mise à jour complète (avec changements) | `sudo apt full-upgrade`                |

---

## 🧹 Supprimer un paquet proprement

| Commande                          | Description                                               |
|----------------------------------|-----------------------------------------------------------|
| `sudo apt remove`                | Supprime uniquement le paquet                             |
| `sudo apt autoremove`            | Supprime les dépendances devenues inutiles                |
| `sudo apt purge`                 | Supprime le paquet + ses fichiers de configuration 🧼     |
| `sudo apt purge && autoremove`   | Supprime tout proprement en une seule commande 🧽         |

---

## 🧪 Options diverses

| Commande                          | Description                                          |
|----------------------------------|------------------------------------------------------|
| `sudo dpkg -i`                   | Installer un paquet local (`.deb`)                  |
| `sudo apt clean`                 | Nettoyer le cache des paquets téléchargés 🧼        |
| `sudo apt download`             | Télécharger un paquet sans l’installer              |
| `sudo apt install -y`           | Ne pas poser de questions (mode automatique)        |
