# Dotfiles Management (Bare Git Repository)

Gestion des fichiers de configuration via un dépôt bare. Cette méthode permet
de garder le $HOME propre tout en versionnant les fichiers.

## Setup Initial

1. Créer le dossier du dépôt :
```
mkdir $HOME/.cfg
```

2. Initialiser le dépôt bare :
```
git init --bare $HOME/.cfg
```

3. Définir l'alias (à ajouter au .bashrc) :
```
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```

4. Ignorer tous les fichiers par défaut :
```
echo "*" > ~/.gitignore
```

5. Connecter le dépôt distant :
```
config remote add origin git@github.com:kennethassogba/baredotfiles
```

## Usage

Pour ajouter et pousser un fichier (le flag -f est requis par le gitignore *) :
```
config add -f .bashrc && config commit -m "Update" && config push -u origin main
```

Pour suivre les modifications :
```
config status
config diff
```

## Installation sur une nouvelle machine

1. Installer git, neovim et configurer l'alias :
```
sudo apt install git neovim
alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
```

2. Empêcher les récursions :
```
echo ".cfg" >> .gitignore
```

3. Cloner le dépôt :
```
git clone --separate-git-dir=$HOME/.cfg git@github.com:kennethassogba/baredotfiles $HOME/cfg-tmp
```

4. Déployer les fichiers (écrase les fichiers par défaut) :
```
cp -rv --backup $HOME/cfg-tmp/. $HOME/
rm -rf $HOME/cfg-tmp
```

5. Masquer les fichiers non suivis pour la propreté :
```
config config --local status.showUntrackedFiles no
```

## Backup des paquets (Installés manuellement)

### Export

```
apt-mark showmanual > packages_apt.txt
npm list -g --depth=0 --json | jq -r '.dependencies | keys[]' > packages_npm.txt
```

### Import

```
sudo apt update
sudo xargs -a packages_apt.txt apt install -y
xargs -a packages_npm.txt npm install -g
```

## NvChad (Ne sauvegarder que le nécessaire)

Ne pas sauvegarder tout le dossier nvim pour éviter les conflits de binaires
et de cache. Sauvegarder uniquement la configuration utilisateur :

config add -f ~/.config/nvim/lua

Lors d'une nouvelle install :

1. Cloner NvChad : `git clone https://github.com/NvChad/starter ~/.config/nvim`
2. Restaurer les dotfiles (qui viendront remplir `lua`).
3. Lancer `nvim` pour l'auto-installation.

