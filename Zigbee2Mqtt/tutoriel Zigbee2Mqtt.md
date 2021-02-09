# Tutoriel de mise en place de zigbee2mqtt dans home assistant
Yann RITTER  Tuto Home Assistant 

Dans ce tutoriel, tu trouveras toutes les informations pour la mise en place de zigbee2mqtt

L'objectif est multiple : 
- Installation et configuration de zigbee2mqtt
- Decouverte automatique avec le brocker MQTT
- Un exemple d'automatisation



## Compatibilité
- Toutes versions de home assistant

## Source 
https://www.zigbee2mqtt.io/



## Avertissement 

Si, tu lis ça ! Non mais, Y’a pas que le tuto qui compte ! On a deux trois bricoles importantes à te dire avant.
Maintenant tu es adulte, t’as bien grandi, alors tu comprends que toutes les manipulations que tu vas faire sont sous ta responsabilité !
Si ton Home Assistant plante, ou que ton Raspberry explose… On n’y est pour rien !
Tout ce qui suit a été d’abord installé, testé par nos soins. Ça fonctionne. Et je l’utilise au quotidien !
Donc tu prends ton temps, tu lis bien, tu ne fais pas l’enfant à vouloir aller trop vite et sauter des étapes en mode « Ouais c’est bon je sais ! »
Ce n’est pas un tuto sur les bases, alors certaines étapes sont pour nous acquises et ne seront pas détaillées ici. Y’a toujours le groupe si vraiment tu as besoin d’aide…
On y va ?


## Prérequis

Disposer d'une clé zigbee compatible zigbee2mqtt
la liste des compatibilités est disponible ici :  https://www.zigbee2mqtt.io/information/supported_adapters.html 

## Ajout d'un utilisateur pour MQTT
Dans le menu configuration / utilisateurs

Creer un nouvel utilisateur spécifique pour zigbee2mqtt.

Par exemple :
- Nom : zigbeemqtt
- Nom d'utilisateur : zigbeemqtt
- Mot de passe : 123456
- Confirmer le mot de passe : 123456
- Décocher l'option : administrateur

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/utilisateur.JPG)


## Installation des addons

Tu vas devoir utiliser Zigbee2mqtt et Mosquitto broker


### Ajout de Mosquitto broker

Navigue dans le menu : Supervisor → Add-on store → Mosquitto broker → installer

Tu devras alors configurer le broker tel que la capture ci-dessous.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/mosquitto.JPG)

Tu peux démarrer l'addon.


### Ajout de Zigbee2Mqtt

Tu devras ajouter l'url du repository pour zigbee2mqtt : Supervisor → Add-on store → ⋮ → Manage add-on repositories:

Ajoute l'URL suivante : https://github.com/danielwelch/hassio-zigbee2mqtt

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt1.JPG)

Maintenant, tu trouveras dans les addons disponibles Zigbee2Mqtt

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt2.JPG)

Clique dessus, puis installer.

Je passe à la partie configuration de l'addon

Il faut renseigner ici 3 points :
- user
- password
- port


Le user et le password doivent correspondre à l'utilisateur crée précédement dans home assistant


- user : zigbeemqtt
- password : 123456

Le port correspond à l'identifiant matériel de la clé zigbee (il faut qu'elle soit connectée sur un port USB du PI de preférence avec une rallonge USB pour limiter les interférences)


Pour l'obtenir il faut naviguer dans Supervisor → System → Host → ⋮ → Hardware

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt4.JPG)


Si je récapitule, tu devrais avoir :

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt3.JPG)


Si oui tu peux sauvegarder la configuration et demarrer l'addon.

Dans les logs de l'addon, tu auras alors le lancement de l'addon sans erreur.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt5.JPG)



## Configuration de l'integration pour la decouverte automatique 

Dans le menu Configuration → Intégration → ajouter l'intégration → rechercher MQTT
Configuration : 

- host : localhost
- user : zigbeemqtt
- mot de passe : 123456

Cliquer sur valider

Les peripheriques ajoutés remonteront alors automatiquement en tant qu'appareils / entités

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt6.JPG)

## Activer la decouverte zigbee

Tu trouveras ici la liste des peripheriques compatibles :  https://www.zigbee2mqtt.io/information/supported_devices.html

Pour pouvoir associer un périphérique zigbee, il est nécessaire d'activer la decouverte dans l'addon zigbee2mqtt

Modifier la valeur → permit_join: true

Sauvegarder la configuration

Redemarrer l'addon.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt7.JPG)

Pour une question de securité, il est conseillé de remettre à false une fois les péripheriques ajoutés 

permit_join: false

Sauvegarder la configuration

Redemarrer l'addon.


## Un exemple d'appareils découvert

Exemple avec un capteur aqara de porte et fenêtre 

Depuis le menu : Configuration → Intégration → MQTT → appareils

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt8.JPG)

et dans le detail de l'appareil

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt9.JPG)


## Une automatisation 

Je poursuis avec un exemple d'automatisation avec ce capteur comme déclencheur

Dans le menu : Configuration → Automatisations → ajouter une automatisation → commencer par une automatisation vide


Définir :
- un nom d'automatisation


Puis dans le déclencheur :

- choisir état comme type de déclencheur
- choisir le sensor provenant de zigbee2mqtt
- definir le changement d'état ici sur une ouverture donc on

Ajouter les conditions souhaités
Ajouter les actions

Sauvegarder et tester votre automatisation.


![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Zigbee2Mqtt/Images/zigbee2mqtt10.JPG)



## Le mot de la fin
Une petite doc, un tuto, qui on espère t’a aidé le mieux possible.
Je l'ai réalisé avec plaisir dans un esprit d’entre-aide, bénévolement, en parallèle de mon métier et ma vie.
Je n'assure pas le support de tous les problèmes mais je serais là si besoin sur le groupe, comme d’habitude.
Si tout fonctionne, remercie-moi.

Yann RITTER
