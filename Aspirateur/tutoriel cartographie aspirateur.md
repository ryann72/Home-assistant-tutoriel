# Tutoriel cartographie aspirateur Xiaomi
Aurel RV / Yann RITTER Tuto Home Assistant 

Quand l’Homme kiffa son aspi !
Intégration des aspirateurs Xiaomi, Roborock à Home Assistant pour avoir la carte en réelle, et gérer à la voix avec vos assistants vocaux

## Compatibilité
- Xiaomi Mi Robot 1S
- Xiaomi Vacuum Gen 1 (Aspirateur Mi Robot / SDJQR01RR / SDJQR02RR)
- Roborock S4 (logiciel avec prise en charge des salles)
- Roborock S5 (logiciel sans prise en charge des salles)
- Roborock S5 (logiciel avec prise en charge des salles)
- Roborock S5 Max
- Roborock S6
- Roborock S6 Max


Un petit tuto qui explique les étapes clés et les détails importants afin d'intégrer vos aspirateurs à Home Assistant, sans dessiner de carte, récupération de la carte réelle, avec suivi du robot etc...
(Sans Flolevac qui ne gère pas les aspis multi-cartes.)
Et le clou du spectacle, gérer vos bêtes à la voix avec Alexa ou Google, par pièces, zones, nombre de passages...

Vous pourrez définir une pièce connue dans Xiaomi, une zone enregistrée, enchainer des pièces ou des zones, ainsi que paramétrer la puissance d'aspiration et le nombre de passages, dans un seul script. Donc, une seule phrase à votre assistant vocal !
Alexa passe la serpillière dans la cuisine : le script peut dire, zone cuisine + 2 passages + puissance mini.


Nous ne sommes pas les DEV des intégrations nécessaires, mais des passionnés ayant assimilés la technique et les différentes manipulations, et souhaitons vous aider en partageant tout ça ! 😉

## Avertissement 

Si, tu lis ça ! Non mais, Y’a pas que le tuto qui compte ! On a deux trois bricoles importantes à te dire avant.
Maintenant tu es adulte, t’as bien grandi, alors tu comprends que toutes les manipulations que tu vas faire sont sous ta responsabilité !
Si ton aspi recule au lieu d’avancer, qu’il danse la samba, qu’il va aspirer la salle de bains au lieu de passer la serpillière dans la cuisine, que ton Home Assistant plante, ou que ton Raspberry explose… On n’y est pour rien !
Tout ce qui suit a été d’abord installé, testé par nos soins. Ça fonctionne. Et on l’utilise au quotidien !
Donc tu prends ton temps, tu lis bien, tu ne fais pas l’enfant à vouloir aller trop vite et sauter des étapes en mode « Ouais c’est bon je sais ! »
Ce n’est pas un tuto sur les bases, alors certaines étapes sont pour nous acquises et ne seront pas détaillées ici. Y’a toujours le groupe si vraiment tu as besoin d’aide…
On y va ?
Tu as déjà un des aspis listés au-dessus intégré à Home Assistant ?
Non ? Alors intègre le c’est indispensable pour continuer, en suivant ce superbe tuto :
https://www.lesalexiens.fr/tutoriels/integrer-aspirateur-robot-xiaomi-roborock-home-assistant/
Bon, t’as bien Home Assistant, un aspi intégré, Hacs, File Editor, Internet, quelques doigts, et un peu de temps… ?
Go !

## Prérequis / Installation des modules
Voici les deux plugins à ajouter via HACS :
https://github.com/PiotrMachowski/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor#attributes-configuration

Pour celui-ci il faut que tu ajoutes le repository dans HACS:
>  Si tu ne sais pas comment faire, tu vas dans configuration / Tableau de bord / ressources, tu cliques sur le +, tu sélectionnes « Module JavaScript » et tu colles l’URL suivante. Parfois un « restart » de Home Assistant est nécessaire pour le retrouver dans HACS… 
>  Ensuite dans HACS, tu pourras le retrouver et l’installer
1. URL: https://github.com/PiotrMachowski/Home-Assistant-custom-components-Xiaomi-Cloud-Map-Extractor 
2. Category: Integration 
 Et :
https://github.com/PiotrMachowski/lovelace-xiaomi-vacuum-map-card
Que tu retrouveras dans la partie frontend de HACS.

## Configuration de l’extraction de la carte
Tu vas devoir utiliser FileEditor ou sambaShare pour modifier ton fichier configuration.yaml,
ajoute la configuration ci-dessous :
> Si tu as déjà « camera : » tu ne le remets pas hein ! Tu colles en dessous…

Nous, on te conseille d’utiliser l’option « secret » pour cacher tes mots de passe, si tu ne maitrises pas tu peux toujours les écrire ici quand même.
Remplace nos commentaires par tes données :
````yaml camera:
- platform: xiaomi_cloud_map_extractor
  host: !secret xiaomi_vacuum_host #secret à créer ou remplacer par l’ip de l’aspirateur
  token: !secret xiaomi_vacuum_token #secret à créer ou remplacer par le token de l’aspirateur
  username: !secret xiaomi_cloud_username #secret à créer ou remplacer par le mail du compte mi-home
  password: !secret xiaomi_cloud_password #secret à créer ou remplacer par le mot de passe du compte mi-home
  name: "Aspirateur" #nommer votre aspirateur
  draw: ['all']
  map_transformation:
    scale: 2 #ratio de l’image à tester en fonction de la taille de la carte souhaité
    rotate: 180 #effectue une rotation de 0, 90, 138, 360 degré de la carte
    trim:
      top: 10 #permet de découper l’image sur le bord haut de façon à optimiser l’espace
      bottom: 20 #permet de découper l’image sur le bord bas de façon à optimiser l’espace
      left: 30 #permet de découper l’image sur le bord gauche de façon à optimiser l’espace
      right: 40 #permet de découper l’image sur le bord droit haut de façon à optimiser l’espace
  sizes:
    charger_radius: 3
    vacuum_radius: 4
  attributes:
    - calibration_points
    - charger
    - goto
    - goto_path
    - goto_predicted_path
    - image
    - is_empty
    - map_name
    - no_go_areas
    - no_mopping_areas
    - obstacles
    - path
    - room_numbers
    - rooms
    - vacuum_position
    - vacuum_room
    - walls
    - zones
  auto_update: true
````

Une fois la configuration réalisée, il faut redémarrer home assistant.
A chaque modification apportée ici, un restart de Home Assistant est nécessaire.
A toi te tâter pour le scale, le rognage, les couleurs…
Consulte la page https://github.com/PiotrMachowski/lovelace-xiaomi-vacuum-map-card pour avoir toutes les infos et les possibilités !

## Consultation des données issues de la carte
Tu sais, le nom que tu as renseigné dans « name : » juste avant, et bien une nouvelle entité est disponible camera.Aspirateur_ou_le_nom_que_tu_as_choisi .
Rends-toi dans Outil de développement / entité.
Et tape « camera.Aspirateur_ou_le_nom_que_tu_as_choisi »
Tu vas voir le détail des informations remontées…
Ça fait peur hein ! Pas de panique on ne va pas servir de tout !
Voici un exemple de résultat :
```yaml 
access_token: 4c9e2de29212XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX9358
calibration_points:
- vacuum:
	x: 25500
	'y': 25500
map:
	x: 240
	'y': 276
- vacuum:
	x: 26500
	'y': 25500
map:
	x: 280
	'y': 276
- vacuum:
	x: 26500
	'y': 26500
map:
	x: 280
	'y': 236
charger:
	x: 25505
	'y': 25221
goto: null
goto_path: null
goto_predicted_path: null
image:
	size: 185136
	offset_y: 356
	offset_x: 390
	height: 293
	scale: 2
	rotation: 0
	width: 320
is_empty: false
map_name: robomap%2F117888850%2F11
no_go_areas: []
no_mopping_areas: null
obstacles: null
path:
	point_length: 96
	point_size: 4
	angle: 0
	path:
	- x: 25547
	'y': 25410
	- x: 25570
	'y': 25510
	- x: 25591
	'y': 25606
	- x: 25617
	'y': 25577
	- x: 25666
	'y': 25520
	- x: 25685
	'y': 25506
	- x: 25717
room_numbers:
	- 18
	- 16
	- 17
	- 2
	- 3
	- 4
rooms: #coordonnées des pièces découpé dans l’application xiaomi
	'2':
		x0: 26700
		y0: 22800
		x1: 34550
		y1: 32300
	'3':
		x0: 26050
		y0: 23500
		x1: 26950
		y1: 25300
	'4':
		x0: 25400
		y0: 24700
		x1: 27000
		y1: 26350
	'16':
		x0: 26050
		y0: 19700
		x1: 30450
		y1: 23800
	'17':
		x0: 19500
		y0: 22450
		x1: 26050
		y1: 26450
	'18':
		x0: 19500
		y0: 19100
		x1: 26900
		y1: 23600
vacuum_position:
	x: 25551
	'y': 25394
	a: 4294967192
vacuum_room: 4
walls: []
zones:
  - x0: 25400
	y0: 24700
	x1: 27000
	y1: 26350
friendly_name: Aspirateur
entity_picture: >-
/api/camera_proxy/camera.aspirateur?token=4c9e2dxxxxXX358
```
## Affichage et configuration dans lovelace
Créer une carte manuelle sur le tableau de ton choix.
Ici aussi tu fais attention… Tu écris bien ce qu’on te dit. 
Regarde la prochaine capture si tu doutes…



````yaml 
type: 'custom:xiaomi-vacuum-map-card'
entity: vacuum.xiaomi_vacuum_cleaner #nom de ton entité aspirateur « vacuum.xxxx »
map_camera: camera.aspirateur  #nom de ton entité caméra vu juste avant…
camera_calibration: true  #permet de récupérer la calibration automatique
language: fr #langue de la carte
modes:
	- predefined_zones
	- go_to_target
	- zoned_cleanup
zones:  #on t’explique après comment obtenir les cordonnées
	- - - 26050
		- 23500
		- 26950
		- 25300
	- - - 26700
		- 22800
		- 34550
		- 32300
	- - - 25400
		- 24700
		- 27000
		- 26350
````
…. Pour toutes les zones de vos choix
Pour savoir quoi écrire comme chiffres pour une zone, deux solutions.
Mais d’abord tu ajoutes « debug : true » dans ta carte.
Cette option permettra plus tard, quand tu cliqueras sur « démarrage », qu’une popup te donne les infos comportant les coordonnées…
Il est important que tu fasses la différence entre rooms et zones.
Rooms : pièces enregistrées dans l’app Xiaomi
Zones : zones tracées et enregistrées dans la carte Home Assistant
Les coordonnées sont toujours un ensemble de 4 nombres, correspondant à ces côtés :
- droite
- haut
- gauche
- bas

#### Les différentes solutions de récupération des coordonnées :
1. Utilisation des coordonnées des Rooms : tu retournes dans outil DEV, et l’entité de ta camera.aspixxx et tu cherches les coordonnées notées sous « rooms » (ce sont les pièces créées dans l’app Xiaomi).

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/coordonnees.jpg)

Tu copies chaque série de 4 nombres pour une zone sous la forme qu’on a indiquée dans la carte…
> REMARQUE : on a vu que parfois ces coordonnées n’étaient pas des plus exactes…
2. Utilisation des zones : tu cliques sur ta jolie nouvelle carte, « mode / nettoyage de zones »
Tu traces une zone…
Tutoriel cartographie aspirateur Xiaomi
Tu cliques sur « démarrage », une popup apparaît !
Tu copies chaque nombre sous la forme indiquée. (Le dernier chiffre isolé ne te servira pas ici…)
« Exemple d’affichage de la pop-up pour la zone sélectionnée »

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/debug.jpg)



3. Tu vérifies en cliquant sur ta jolie nouvelle carte, « mode / zones », tout ce qui est enregistré !
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/exemple-zone.jpg)
4. Tu peux maintenant changer la valeur de « debug » par « false » quand tu as fini.
Voici le résultat avec les différentes zones 

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/exemple1.jpg)

et ici avec deux zones sélectionnées (verte)

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/exemple2.jpg)

## Automation ou script pour nettoyage par pièce ou zone.
Tu te souviens de la différence entre Rooms et zone ?
Car on ne va pas appeler le même service pour chacun, et sache que seul le service zone te permettra de choisir aussi le nombre de passages !
### ZONE ENREGISTREE :
Pour réaliser une automation ou un script permettant le nettoyage d’une zone définie, il faut appeler le service suivant : 
```` yaml
service: xiaomi_miio.vacuum_clean_zone 
data: 
  zone: #coordonnées de la zone tel qu’insérées dans la carte 
	- - 27333 
	  - 28810 
	  - 28777 
	  - 33623 
repeats: 2 #nombre de répétition souhaité 
entity_id: vacuum.xiaomi_vacuum_cleaner
````
C’est la ligne « repeats : » qui détermine le nombre de passages, 3 maxi
Tu peux bien sur enchaîner les zones dans le même script, en respectant la mise en forme des coordonnées.
### ROOMS – PIECES :
Et comment utiliser les pièces de l’app Xiaomi ? On t’explique ça ! Pour réaliser une automation ou un script permettant le nettoyage d’une pièce enregistrée dans l’app Xiaomi, il faut
D’abord retourner dans outil DEV, entité de la camera.aspi… Tu vois de quoi on parle maintenant ?
Là tu cherches les rooms_number.
Chaque numéro correspond à une pièce, tu vas regarder à quelle pièce donc correspond un numéro en envoyant bosser ton aspi !
Dans outil DEV onglet « service » tu ouvres le service « vacuum.send_command » et tu renseignes comme ça :
```` yaml 
entity_id: vacuum.wall_e
command: app_segment_clean
params:
	- 23
````

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/outilsdev.jpg)

Regarde sur la carte, même de l’app Xiaomi peu importe, où il va et quelle pièce se met en surbrillance… Bingo ! Tu as la correspondance ! Stoppe-le, il va assez tourner en rond le temps que tu fasses chaque numéro, ne le fatigue pas pour rien !
Fais-toi un script comme ceci, avec le même service qu’on vient d’utiliser, imagine que moi c’est ma cuisine, je fais ça : 
```` yaml 
aspi_walle_cuisine: alias: Aspi walle cuisine 
sequence: 
- service: vacuum.send_command 
  data: 
  entity_id: vacuum.wall_e 
  command: app_segment_clean 
  params: - 23 
  entity_id: vacuum.wall_e
````
Après un rafraîchissement de tes scripts, tu le retrouveras sous le nom « Aspi walle cuisine » pour cet exemple !
Tu peux enchainer les rooms number dans un même script !
Exemple : 
````yaml 
aspi_walle_couloir: 
alias: Aspi walle couloir et entrée 
sequence: 
- service: vacuum.send_command 
	data: 
	  entity_id: vacuum.wall_e 
	  command: app_segment_clean 
	  params: 
		- 25 
		- 18 
entity_id: vacuum.wall_e
````

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/code.jpg)

Seul hic, pas de répétition possible pour les nombres de passages.
### Combo !
Maintenant que tu en es là tu peux mixer les commandes dans un même script pour aussi gérer la puissance d’aspiration de quasi nul en mode serpillère, à très fort !
- Gentle
- Silent
- Stantard
- Medium
- Turbo
Appelle le service « vacuum.set_fan_speed » pour ça.
Et mixe ! Exemple personnel, je souhaite deux passages pour ma cuisine, car c’est dédié à la serpillère, avec puissance mini :
```` yaml 
aspi_walle_serpille_cuisine:
	alias: Aspi walle serpillière cuisine
	sequence:
	- service: vacuum.set_fan_speed
	  data:
	  fan_speed: Gentle
	  entity_id: vacuum.wall_e
	- service: xiaomi_miio.vacuum_clean_zone
	  data:
	  zone:
		- - 23761
		  - 28734
		  - 27308
		  - 31571
	  repeats: 2
	  entity_id: vacuum.wall_e
	  mode: single
````

A toi de faire autant de scripts que de situations que tu as besoin…

## Assistants Vocaux
Les scripts sont terminés ?
Et bien injecte les dans Alexa ou Google, et enjoy !!!

Il y a 4 solutions afin d’intégrer les assistants vocaux à home assistant mais ce n’est pas l’objet du présent tuto.
Nous partons du principe que ton HA est déjà connecté à ton assistant préféré.
Alexa (et Google Assistant), via l’abonnement Nabu Casa

Je te parle ici de cette méthode étant celle que j’utilise, mon complice te raconte la méthode Google sans Nabu Casa juste après ! Bien entendu tu as intégré Alexa, sinon revient après… !
Rends-toi dans Configuration / Cloud Home Assistant / Alexa / Gérer les entités

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/alexa1.jpg)

Tu sélectionnes celles que tu veux injecter, logique… ! « Exposer l’entité »

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/alexa2.jpg)

Reviens en arrière et tu valides « synchroniser les entités »
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/alexa3.jpg)

Tadaaam !!

Normalement tu reçois instantanément une notification de ton app Alexa comme quoi des nouvelles choses sont arrivées…
Oui, ouvre l’app Alexa !
Où retrouves-tu tout ça ?
Deux secondes je t’explique… je n’écris pas aussi vite que tu lis !
Tu vas créer une nouvelle routine.
Et dans actions tu vas chercher « maison connectée », puis « contrôler une scène ».
Tadaaaam !
Tous tes scripts, automatisations sont là !

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Aspirateur/Images/alexa4.jpg)

T’as plus qu’à choisir le bon, et te faire une petite phrase de déclenchement.
Pour ma part, ayant essayé sans succès « passe l’aspi », j’ai choisi « passe l’aspirateur dans xx » ou « passe la serpillière dans xx ».
> Astuce : dans Alexa, (ou Google), n’enregistre pas ton robot dans une pièce style « salon » ! 

Car « Alexa allume le salon » va déclencher ton robot et tu vas encore râler car ça fait trop de bruit pendant que tu regardes ta série, et que les enfants dorment, et tu vas dire « c’est de leur faute avec leur tuto de…. »
Crée une pièce genre « Aspi » et colle-le là. Tu nous remercieras.
### Google Assistant sans abonnement Nabucasa
Dans le configuration.yaml, tu dois ajouter le / les scripts sous le bloc suivant (si tu n’as pas ce bloc de code c’est que tu n’as pas lu la consigne) :
````yaml
google_assistant:
	secure_devices_pin: XX
	project_id: yyyy-XXX
	service_account: !include yyyy-XX.json
	report_state: true
	exposed_domains:
		- switch
		- light
		- vacuum
		- cover
		- alarm_control_panel
	entity_config: #uniquement modifier la liste ci-dessous afin d’ajouter vos scripts
		script.aspirateur_cuisine: #le nom de script créée
		expose: true
````
Sauvegarde le fichier configuration.yaml
Redémarre home assistant pour prise en compte du nouveau paramétrage
Attends le redémarrage complet de home assistant avant de crier « OK GOOGLE synchronise mes appareils » parce que tu es pressé de tester la nouvelle fonctionnalité.
Dans Google assistant tu devrais voir apparaitre les scripts.
Astuce : ne pas associer les scripts à la pièce concernée dans Google assistant. Sinon tu risques de voir danser ton aspirateur préféré quand tu vas demander à Google d’allumer le salon pour boire ta bière, tranquillement installé dans ton canapé.

## Sécurisation (recorder)

Nous avons remarqué que la cartographie va gonfler la base de données home assistant avec les cooordonnées recupérées.
Tu trouveras alors un tuto fun et sympa permettant de limiter la volumétrie en base de données

https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Recorder/tutoriel%20recorder.md


## Retours persos
Depuis que nous utilisons cette méthode, je me sers à 90% de mon assistant vocal.
J’ai différencié les phrases « passe l’aspirateur dans… » : 1 passage (room) puissance standard,
Et « passe la serpillière dans… » : 2 passages (zones) puissance douce.
J’ai aussi les commandes vocales pour mettre en pause et retourner à la base. Le tout fait que le robot est réellement pilotable à la voix, sur mesure.
Tout le monde peut maintenant passer l’aspi, même les enfants !

## Le mot de la fin
Une petite doc, un tuto, qui on espère t’a aidé le mieux possible.
Nous l’avons réalisé avec plaisir dans un esprit d’entre-aide, bénévolement, en parallèle de nos métiers et nos vies (si on en a !).
Nous n’assurons pas le support de tous les problèmes mais nous seront là si besoin sur le groupe, comme d’habitude.
Si tout fonctionne, remercie-moi.
Mais sinon, c’est forcément de sa faute.
Qui écrit ? Lui ou moi.
En ce moment ? Forcément moi, parfois lui.
Deux potes passionnés, Yann Ritter et Aurel RV.
