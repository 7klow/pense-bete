# 🐧 Pense-bête `pacman` – Manjaro / Arch Linux

Un mémo rapide et clair pour utiliser `pacman`, le gestionnaire de paquets 🧠

---

## 🛠️ Commandes principales

| Option | Description                            | Exemple                              |
|--------|----------------------------------------|--------------------------------------|
| `-S`   | 🔽 Installer un paquet                  | `sudo pacman -S firefox`             |
| `-R`   | 🗑️ Supprimer un paquet                 | `sudo pacman -R firefox`             |
| `-Q`   | 🔍 Interroger un paquet (installé)     | `pacman -Q firefox`                  |
| `-Ss`  | 🔍 Rechercher dans les dépôts          | `pacman -Ss vlc`                     |
| `-Qs`  | 🔍 Rechercher dans les paquets installés | `pacman -Qs code`                 |
| `-Si`  | 📄 Infos d’un paquet (dans les dépôts) | `pacman -Si vlc`                     |
| `-Qi`  | 📄 Infos d’un paquet installé          | `pacman -Qi vlc`                     |
| `-Sy`  | 🔄 Met à jour la base des paquets      | `sudo pacman -Sy`                    |
| `-Syu` | 🆙 Mise à jour complète du système      | `sudo pacman -Syu`                   |

---

## 🧹 Supprimer un paquet proprement

| Option    | Description                                                              |
|-----------|---------------------------------------------------------------------------|
| `-R`      | Supprime uniquement le paquet                                            |
| `-Rs`     | Supprime le paquet + ses dépendances inutilisées                         |
| `-Rns`    | Supprime le paquet + dépendances inutiles + fichiers de config locaux 🧼 |

---

## 🧪 Options diverses

| Option        | Description                                           |
|---------------|------------------------------------------------------|
| `-U`          | Installer un paquet local (`.pkg.tar.zst`)           |
| `-F`          | Travailler avec la base de fichiers (ex: fichiers manquants) |
| `-Sc`         | Nettoyer le cache des paquets téléchargés 🧼         |
| `-Sw`         | Télécharger un paquet sans l’installer               |
| `--noconfirm` | Ne pas poser de questions (mode automatique)         |
