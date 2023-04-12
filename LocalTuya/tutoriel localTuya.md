# Tutoriel mise en place de local tuya  
Yann RITTER  Tuto Home Assistant 

Dans ce tutoriel, tu trouveras les informations nécéssaires à la mise en place de localtuya

L'objectif : ne plus etre dépendant du cloud tuya



## Compatibilité
- Toutes versions de home assistant après la 0.7

## Source 
https://github.com/rospogrigio/localtuya

Pour le moment l'integration permet la prise en charge des types d'appareils suivant ;
- 1 and multiple gang switches
- Wi-Fi smart plugs (including those with additional USB plugs)
- Lights
- Covers
- Fans

## Avertissement 

Si, tu lis ça ! Non mais, Y’a pas que le tuto qui compte ! On a deux trois bricoles importantes à te dire avant.
Maintenant tu es adulte, t’as bien grandi, alors tu comprends que toutes les manipulations que tu vas faire sont sous ta responsabilité !
Si ton Home Assistant plante, ou que ton Raspberry explose… On n’y est pour rien !
Tout ce qui suit a été d’abord installé, testé par nos soins. Ça fonctionne. Et on l’utilise au quotidien !
Donc tu prends ton temps, tu lis bien, tu ne fais pas l’enfant à vouloir aller trop vite et sauter des étapes en mode « Ouais c’est bon je sais ! »
Ce n’est pas un tuto sur les bases, alors certaines étapes sont pour nous acquises et ne seront pas détaillées ici. Y’a toujours le groupe si vraiment tu as besoin d’aide…
On y va ?


## Prérequis

- Installer la version desktop de potman disponible ici gratuitement : https://www.postman.com/downloads/ 

- Télécharger les deux archives du dossier : https://github.com/ryann72/Home-assistant-tutoriel/tree/main/LocalTuya/postman 

- Disposer d'un smartphone avec l'application Tuya

- Hacs installé sur votre home assistant
 
## Recherche des local keys tuya

Afin de récupérer les local keys tuya, nous devons ouvrir un compte développeur sur l'environnement Tuya


### 1 - Création du compte

Aller sur le site : https://iot.tuya.com/ et creer un compte

La page une fois connecté : 
![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/iot.JPG)

### 2 - Création du projet

Naviguer dans le menu de gauche pour aller sur "Cloud"

Puis "Project"

Et le bouton "Create"

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/creerProjet.JPG)

Saisir alors les informations de votre choix.

Par exemple : 
- Project Name : test tuto
- Description : mon projet de démo pour le tuto
- Industry : Smart Home

Valider avec le bouton "create"

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/creerProjet2.JPG)


En selectionnant le projet précédement crée, tu obtiendras alors les informations suivantes 

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/creerProjet3.JPG)

Tu auras besoin de récupérer ici les informations d'autorization key pour postman. 

!!! J'y reviendrais par la suite !!!


### 3 - Lien avec le compte tuya sur smartphone

Tu devras à présent lier ton compte tuya au compte de dev crée ici afin de retrouver les périphériques.

Naviguer dans le menu de gauche pour aller sur "Cloud" --> "Device" --> Linked Device --> Link devices by App Account


Image de l'écran attendu :

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/creerProjet4.JPG)

Clique sur le bouton  "Add App Account"

Un QRcode à scanner est alors affiché

Scan le avec ton smartphone dans l'application tuya.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/Capture5.JPG) 


### 4 - Remontées de périphériques connus

La connexion étant valide, les peripheriques rattachés au compte remontent tel que l'affichage suivant.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/Capture6.JPG) 

### 5 - Activation de l'API group

Naviguer dans le menu de gauche pour aller sur "Cloud" puis sur "API Group" et activer le "Device Management"

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/Capture7.JPG) 

Cette activation va nous permettre de communiquer avec postman sur les serveurs Tuya.



Nous en avons terminé sur la partie cloud de tuya.

Passons sur postman afin de récupérer la local keys.


## Utilisation de Postman

Tu devras decompresser les deux archives téléchargées en pré-requis.
Puis les importer dans postman.

Utiliser le bouton import  - voir capture d'écran

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/postman2.JPG)

et importer OpenAPI_postman_collection

Importer l'environnement - voir capture d'écran

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/postman3.JPG)


Tu vas devoir récupérer les données dont j'ai parlé précédemment afin de pouvoir réaliser l'authentification.

Renseigner les données spécifiques à ton compte disponible ici :

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/creerProjet3.JPG)

### 1 - Récupération du token d'identifiction
Dans postman 
- client_id
- secret

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/postman5.JPG)

puis appeler le service "acces token" et tu devrais avoir un résultat similaire.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/postman6.JPG)

### 2 - Récupération du device id 

Il faut définir le device ID dans la configuration postman.

Cette id est disponible dans l'écran ci-dessous

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/Capture6.JPG)  

et le renseigner ici :

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/postman7.JPG)


### 3 - Récuperation de la local keys 


Appele le service "{{url}}/v1.0/devices/{{device_id}}" 

La local key est disponible dans la réponse : 

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/postman4.JPG)

Note bien le couple device id / local key, il te sera nécessaire pour la suite.

Tu peux refaire la procédure pour récupérer les tokens de tous tes appareils Tuya.


## Intégration dans home assistant 

Dans home assistant, tu devras installer local Tuya depuis HACS.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/hacs.JPG)

Puis dans le menu Configuration --> Intégration --> ajouter l'intégration --> LocalTuya intégration

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/hacs2.JPG)


Tu arriveras sur le menu suivant

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/integration1.JPG)


Deux options:

1- Le device est trouvé alors tu le sélectionne dans la liste

2- Il n'est pas trouvé et tu choisi ...

Il reste à completer les données correspondantes :

- Name : nom de ton choix pour l'entité
- Local key : celle extraite du dernier appel de web service
- Host : adresse ip (attention de bien la fixer dans la box)
- Device id : (pré-remplie) ou manuel, valeur disponible lors de l'appel du dernier web service
- Protocol : 3.3 par defaut

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/LocalTuya/Images/integration2.JPG)


## Attention 

Il n'est pas possible de cumuler l'intégration cloud et local de tuya sur home assistant.

J'ai également remarqué que lors de certains reboot / mise à jour, il était nécessaire de cliquer sur recharger au niveau de l'intégration local tuya afin de retrouver en ligne son appareil.


## Le mot de la fin
Une petite doc, un tuto, qui je l'espère t’a aidé le mieux possible.
Je l'ai réalisé avec plaisir dans un esprit d’entre-aide, bénévolement, en parallèle de mon métier et ma vie.
Je n'assure pas le support de tous les problèmes mais je serais là si besoin sur le groupe, comme d’habitude.
Si tout fonctionne, remercie-moi.

Yann RITTER

# Remerciement 
Si vous aimez mes tutos, likez 🌟 mon repo !

Si vous souhaitez m'offrir une petite bière ou un café ou participer à l'investissement materiel :  https://www.paypal.com/paypalme/YannRitter  Merci !
