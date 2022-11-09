# Tutoriel BSB LAN
Yann RITTER Tuto Home Assistant 

Quand le controle de la PAC devient possible!
Intégration d'une pompe a chaleur atlantic Alfea extentia duo

## Compatibilité
Voir la liste disponible ici : https://github.com/fredlcore/BSB-LAN


Un petit tuto qui explique les étapes clés et les détails importants afin d'intégrer votre moyen de chauffage dans home assistant.
Je remercie Frederik initateur du projet pour ces travaux et sa réactivité.
Le git du projet  : https://github.com/fredlcore/BSB-LAN


Je ne suis pas le DEV des intégrations nécessaires, mais juste un passionnés ayant assimilé la technique et les différentes manipulations et je souhaite vous aider en partageant tout ça ! 😉

## Avertissement 

Si, tu lis ça ! Non mais, Y’a pas que le tuto qui compte ! On a deux trois bricoles importantes à te dire avant.
Maintenant tu es adulte, t’as bien grandi, alors tu comprends que toutes les manipulations que tu vas faire sont sous ta responsabilité !
Si ton aspi recule au lieu d’avancer, qu’il danse la samba, qu’il va aspirer la salle de bains au lieu de passer la serpillière dans la cuisine, que ton Home Assistant plante, ou que ton Raspberry explose… On n’y est pour rien !
Tout ce qui suit a été d’abord installé, testé par nos soins. Ça fonctionne. Et on l’utilise au quotidien !
Donc tu prends ton temps, tu lis bien, tu ne fais pas l’enfant à vouloir aller trop vite et sauter des étapes en mode « Ouais c’est bon je sais ! »
Ce n’est pas un tuto sur les bases, alors certaines étapes sont pour nous acquises et ne seront pas détaillées ici. Y’a toujours le groupe si vraiment tu as besoin d’aide…
On y va ?


## Prérequis / Installation des modules
Avoir Mosquitto broker d'installé
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/Mosquitto.JPG)

J'utilise la configuration par défaut du module
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/Mosquitto1.JPG)
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/Mosquitto2.JPG)



Tu as un système de chauffage compatible et acheté / branché la carte à ton système de chauffage ?
Si non, tu peux consulter le git du projet (https://github.com/fredlcore/BSB-LAN) et tu peux contacter Frederik par e-mail en Allemand ou Anglais à l'adresse suivante : bsb@code-it.de

Si oui, tu peux continuer la lecture
Dans mon cas, j'utilise la version ESP32 : https://1coderookie.github.io/BSB-LPB-LAN_EN/QSG_ESP32.html
Et je souhaite faire communiquer le module BSB-LAN via MQTT, ainsi le module me pousse les valeurs toutes les X secondes et lors de changement.

Etape 1 :
Creer ou réutiliser un utilisateur home assitant pour permettre l'authentification MQTT.
Dans homme assistant aller dans Paramètre\Personnes puis onglet utilisateur
Cliquer le bouton creer un utilisateur
et completer comme ci-dessous
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/utilisateur.JPG)

Etape 2 :

Dans Paramètre \ Integration s'assurer de bien avoir l'integration MQTT, sinon l'ajouter 
broker : localhost
port : 1883
utilisateur : celui cree précédement
mot de passe : idem celui lié a l'utilisateur
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/haintegration.JPG)

Etape 3 :

Nous devons à présent parametrer le module BSB-LAN
Depuis un navigateur web : http://bsb-lan.local/
la page suivante doit s'afficher
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/bsblan0.JPG)

Cliquer ensuite sur le menu Setting 
et modifier les paramètres ci-dessous :
 - La configuration avancée va permettre de saisir le paramétrage MQTT.
 - Le write access lui de permettre la modification depuis l'interface web et MQTT / HTTP

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/bsblan1.JPG)

Puis nous allons modifier les paramètres spécifiques MQTT

Logging mode --> activer le mqtt
Interval (seconds) --> délais maxi entre chaque remontés
Parameters --> liste des paramètre que vous souhaitez remonter / utiliser dans HA la liste complete est disponible via le menu Heater Function
Usage --> format du message mqtt, le plus simple (plain text)
IP address broker --> l'adresse ip de votre serveur mosquitto (dans mon cas mon instance HA)
username --> nom d'utilisateur pour la connexion MQTT, donc celui créé a l'étape précédente dans HA
Password --> le mot de passe associé à l'utilisateur

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/bsblan2.jpg)

Nous pouvons controler les remontés via un MQTT explorer par exemple
http://mqtt-explorer.com/

se reconnecter avec le user / mot de passe sur l'adresse ip du brocker mqtt et normalement les paramètres remontées s'affiche.
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/mqttexplorer.jpg)

Nous alons donc pouvoir les recuperer dans Home assistant.

Dans le fichier configuration.yaml ajouter 
tel que ci-dessous :
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/configurationha1.jpg)


````

   ############################################################
   ##                         MQTT                           ##
   ############################################################
mqtt:
    sensor: !include mqtt/mqtt.yaml
     
````


Puis dans le dossier config creer un dossier mqtt, et un fichier mqtt.yaml

Et ajouter la configuration suivante pour permettre la remontée des paramètres précédement renseigné dans BSB-LAN.

````
   ############################################################
   ##                         MQTT  BSB LAN                  ##
   ############################################################
    - name: "Mode de chauffe"
      state_topic: "BSB-LAN/700.00"
      
    - name: "Mode ESC"
      state_topic: "BSB-LAN/1600.00"
      
    - name: "Température consigne confort"
      state_topic: "BSB-LAN/710.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température consigne reduit"
      state_topic: "BSB-LAN/712.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température sonde exterieur"
      state_topic: "BSB-LAN/8700.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température thermosat interieur"
      state_topic: "BSB-LAN/8740.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Etat du circuit de chauffage"
      state_topic: "BSB-LAN/8000.00"
      
    - name: "Etat de l'ECS"
      state_topic: "BSB-LAN/8003.00"
      
    - name: "Etat pompe à chaleur"
      state_topic: "BSB-LAN/8006.00"
      
    - name: "Température de retour pompe à chaleur"
      state_topic: "BSB-LAN/8410.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Température de départ pompe à chaleur"
      state_topic: "BSB-LAN/8412.00"
      unit_of_measurement: °C
      device_class: temperature
      
    - name: "Etat pompe ECS"
      state_topic: "BSB-LAN/8820.00"
      
    - name: "Etat résistance électrique ECS"
      state_topic: "BSB-LAN/8821.00"
      
    - name: "Température mesurée ECS"
      state_topic: "BSB-LAN/8830.00"
      unit_of_measurement: °C
      device_class: temperature
````

Sauvegarder la configuration puis redemarrer home assistant pour prise en compte du paramétrage

Tu as maintenant les valeurs disponibles dans des sensor pour home assistant
Voici un exemple rapide : 
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/haresultat1.jpg)


Je vais a present tester la modification d'une valeur via MQTT pour passer du mode hors gel en mode automatique

pour ce faire, je doit envoyer 1 sur le paramètre 700.
Depuis l'intagration MQTT il est possible de tester la publication d'un paquet
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/testpaquet.jpg)

Tu peux constater que le mode de chauffage à basculé en mode automatique
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/testpaquetresultat.jpg)

Je vais a present te montrer comment changer l'état depuis un bouton virtuel dans HA

Dans le configuration.yaml ajouter la liste button comme ci-dessous
````

   ############################################################
   ##                         MQTT                           ##
   ############################################################
mqtt:
    sensor: !include mqtt/mqtt.yaml
    button: !include mqtt/button.yaml
     
````

Puis creer le fichier button.yaml dans le dossier mqtt

et ajouter le contenu suivant : 

````

   ############################################################
   ##                         MQTT  BSB lan bouton           ##
   ############################################################
    - unique_id: "PAC mode auto"
      name: "PAC mode auto"
      command_topic: "BSB-LAN"
      payload_press: "S700=1"
      availability:
        - topic: "BSB-LAN/status"
      qos: 0
      retain: false
      entity_category: "config"
````

Explication : 
command_topic : sujet a appeler 
payload_press : commande a envoyer lors du clique 
/S pour de la modification puis le code element a modifier = la valeur a renseigner
entity_category : dit que tu agit sur de la configuration
     availability:
        - topic: "BSB-LAN/status" permet de gerer si sensor non disponible

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/BSB-LAN/Images/haresultat2.jpg)


## Retours persos


## Le mot de la fin
Une petite doc, un tuto, qui je l'espère t’a aidé le mieux possible.
Je l'ai réalisé avec plaisir dans un esprit d’entre-aide, bénévolement, en parallèle de mon métier et ma vie.
Je n'assure pas le support de tous les problèmes mais je serais là si besoin sur le groupe, comme d’habitude.
Si tout fonctionne, remercie-moi.

Yann Ritter