Ce thème graphique est une traduction en français du thème par défaut de weeWX v3.5.0 qui se nomme Standard. Ce thème devrait fonctionner avec weewx 3.3.0 ou plus récent.

### Téléchargement

https://github.com/weewx/weewx/files/338169/Standard-FR-v1.0.1.tar.gz

### Installation

1. Arrêtez le processus weeWX :

```
sudo /etc/init.d/weewx stop
```

2. Récupérez le fichier :

```
cd /etc/weewx/skins/
wget https://github.com/weewx/weewx/files/293536/Standard-FR-v1.0.1.tar.gz
tar xzvf Standard-FR-v1.0.1.tar.gz
chown root:root Standard-FR
```

3. Activez l'image du radar météo (optionnel). Par défaut, les images des nuages prise par satellite météo viennent du site web meteo60.fr et sont activées. Si vous voulez les images des nuages prise par satellite météo provenant du site web sat24.com, éditer le fichier `skin.conf` et décommentez les lignes :
```
    # radar_img = http://37.59.123.0/sat/anim-msg-fr-vis.gif
    # radar_url = http://fr.sat24.com/fr/fr/visual
```
et commentez les lignes :
```
    radar_img = http://www.meteo60.fr/satellites/animation-satellite-visible-france.gif
    radar_url = http://www.meteo60.fr/sat_vis.php
```
4. Configurez weeWX pour utiliser le thème en français. Editez le fichier `weewx.conf`, et dans la section `[[StandardReport]]`, changez

```
 skin = Standard
```
par
```
 skin = Standard-FR
```

5. Démarrez le processus weewx:

```
sudo /etc/init.d/weewx start
```

6. Allez sur la page d'accueil de l'URL de weewx et vérifiez la page d'accueil.

### Capture d'écran
![weewx-french-screenshot-2](https://cloud.githubusercontent.com/assets/446723/15724786/f4bad8ec-2848-11e6-82b4-f9b9336e78bb.png)