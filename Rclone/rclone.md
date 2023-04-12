# Tutoriel sauvegarde cloud
Yann RITTER Tuto Home Assistant 

Utilisation de rclone pour permettre la sauvegarde cloud de n'importe quel fichier home assistant.
Par exemple des images / vidéos issues de vos caméras de surveillance.

## Compatibilité
- Toutes versions de home-assistant
- Testé à partir de la version 2023-03-06

Un petit tuto qui explique les étapes clés et les détails importants afin de sauvegarder vos documents et images ... dans un cloud.


Je ne suis pas le DEV des intégrations nécessaires.Je souhaite vous aider en partageant tout ça ! 😉


## Prérequis

Choisir le cloud sur lequel sauvegarder vos données.
La liste des clouds compatibles est disponible ici, il y en a une quarantaine : https://rclone.org/


## Mise en place 

### Configuration du module 


Dans mon cas j'ai choisi google drive
Voici donc la procédure pour obtenir le client id / client secret et token

https://rclone.org/drive/#making-your-own-client-id

A savoir que sous HAos, je n'ai pas réussi à générer le token
J'ai donc fait une installation rclone sous windows afin de récupérer le fichier de configuration.

### Integration dans home-assistant 


Dans mon cas j'utilise HAos, il n'est donc pas aussi simple d'installer et d'acceder au système linux.

Créer un fichier nommé par exemple : rclonePhotos.sh
Le placer à la racine du dossier /config/ de home-assistant puis coller les élements suivants :
'''
#permets d'installer rclone
apk add rclone
#permets de copier la config
mkdir -p /root/.config/rclone/
cp /config/rclone.conf /root/.config/rclone/rclone.conf
#permets de synchroniser les données avec repertoire source et destination paramétrable
rclone sync "${1}" "${2}"
#Purge du dossier local pour les fichiers de plus de 7 jours
find "${1}" -depth -mindepth 1 -mtime +7 -delete
'''


Copier le fichier rclone.conf, dans mon cas généré depuis windows dans le dossier config/ de home-assistant

Exemple de contenu du fichier rclone.conf
'''
[google-drive]
type = drive
client_id = XXXXXXXXXX.apps.googleusercontent.com
scope = drive
client_secret = YYYYYY-YYYYYYYYYYYYYYYYYYYYYYY
token = {"access_token":"montoken","token_type":"Bearer","refresh_token":"montoken2","expiry":"2023-03-31T12:49:29.1598145+02:00"}
team_drive = 

'''

Dans le fichier configuration.yaml

Créer les commandes shell permettant de copier les fichiers

Dans l'exemple ci-dessous la commande exécutera notre fichier script shell rclonePhotos.sh du dossier config avec en répertoire source : /media/Images et en destination un dossier photos à la racine de google drive.
Cela effectuera alors la synchronisation de l'ensemble des medias du dossier Images vers google drive puis supprimera en local les fichiers de plus de 7 jours.

Il est bien evidement possible de supprimer cette purge automatique ou de l'adapter en modifiant le rclonePhotos.sh.

Mon espace cloud n'étant pas ilimité j'ai preféré garder environ 7 jours d'historique seulement. 

'''
shell_command:
  sauvegarde_google_drive_images: /config/rclonePhotos.sh /media/Images google-drive:photos
  sauvegarde_google_drive_videos: /config/rclonePhotos.sh /media/Vidéos google-drive:videos
'''

###Exemple d'automatisation 

Celle-ci permet de prendre une vidéo sur déclenchement d'un capteur d'ouverure
de l'envoyer sur telegram puis d'uploader le fichier sur google-drive
'''
- alias: 'Prendre une video camera jardin'
  id: '1640957225913' 
  trigger:
      - platform: state
        entity_id: 
          - binary_sensor.cabane_jardin_contact
          - binary_sensor.portail_contact
          - binary_sensor.porte_entree_contact
        to: 'on'
  action:
    - delay: '00:00:05'
    - service: camera.record
      data:
        lookback: 15
        filename: /media/Vidéos/detection-jardin.mp4
        duration: 20
      target:
        entity_id: camera.exterieur
    - delay:
        hours: 0
        minutes: 0
        seconds: 40
        milliseconds: 0
    - service: notify.telegram_notification
      data:
        title: 'Jardin 📸'
        message: 'Mouvement détecté !'
        data:
          video:
            - file: '/media/Vidéos/detection-jardin.mp4'
              caption: Vidéo - Mouvement jardin détecté - {{now().strftime('%H:%M %d-%m-%Y')}}
              timeout: 180
    - service: shell_command.sauvegarde_google_drive_videos  
'''