# Tutoriel de mise en place de Samba-Share dans home assistant
Yann RITTER  Tuto Home Assistant 

Dans ce tutoriel, tu trouveras toutes les informations pour la mise en place de Samba-Share

L'objectif est multiple : 
- Installation et configuration de Samba-Share
- Configuration Windows pour l'accès




## Compatibilité
- Toutes versions de home assistant



## Avertissement 

Si, tu lis ça ! Non mais, Y’a pas que le tuto qui compte ! On a deux trois bricoles importantes à te dire avant.
Maintenant tu es adulte, t’as bien grandi, alors tu comprends que toutes les manipulations que tu vas faire sont sous ta responsabilité !
Si ton Home Assistant plante, ou que ton Raspberry explose… On n’y est pour rien !
Tout ce qui suit a été d’abord installé, testé par nos soins. Ça fonctionne. Et je l’utilise au quotidien !
Donc tu prends ton temps, tu lis bien, tu ne fais pas l’enfant à vouloir aller trop vite et sauter des étapes en mode « Ouais c’est bon je sais ! »
Ce n’est pas un tuto sur les bases, alors certaines étapes sont pour nous acquises et ne seront pas détaillées ici. Y’a toujours le groupe si vraiment tu as besoin d’aide…
On y va ?


## Prérequis

Disposer d'une installation home assistant

## Utilité de Samba-Share

Samba est un protocole de partage de fichiers.
Il permet d’accéder aux fichiers de Home Assistant quel que soit le système sur lequel il est installé.
L’explorateur de fichiers de Windows,de macOS ou de n’importe quelle distribution Linux.


## Ajout de Samba-Share
Dans le menu configuration / Modules complémentaires, Sauvegardes et superviseur
Puis dans l'onglet Modules complementaires rechercher Samba dans la boutique des modules complémentaire

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Samba-Share/Images/module1.png)


## Configuration de l'addon

Tu vas devoir configurer l'addon Samba-Share

Tu devras alors configurer l'addon tel que la capture ci-dessous.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Samba-Share/Images/module2.PNG)

Pour plus de praticité, tu peux mettre ton login et mot de passe Windows cela permettra la connexion automatique

Tu peux également restreindre la connexion à certaines adresses IP

allow_hosts:
  - 192.168.0.0/16 --> toutes les ip du réseau courant (configuration générale des box)
  
allow_hosts:
  - 192.168.0.2 --> uniquement la machine avec l'IP défini


Tu peux sauvegarder et démarrer l'addon.


### Controle coté Windows 

Sur Windows 8 et 10, pour vérifier si SMB1 est activé tu devras utiliser la commande suivante en PowerShell en mode administrateur

Get-SmbServerConfiguration | Select EnableSMB2Protocol

voici le résultat si le module samba est activé.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Samba-Share/Images/powershell1.PNG)

S'il n'est pas activé : 

Ouvrez le Panneau de configuration, puis ouvrez Programmes et fonctionnalités. 

Ensuite, sélectionnez Activer ou désactiver les fonctionnalités Windows. 

Faites défiler la liste pour trouver la prise en charge du partage de fichiers SMB Direct.

Activez-le (cochez la case) s’il n’est pas déjà activé.

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Samba-Share/Images/windows1.PNG)


Vous pouvez à présent tester la connexion via un explorateur de fichier
 
 \\ip du serveur HA

Une fenetre de connexion s'ouvre si le user / mot de passe est différent de la session courante
sinon vous avez l'acces tel que 

![alt text](https://github.com/ryann72/Home-assistant-tutoriel/blob/main/Samba-Share/Images/windows2.PNG)


## Le mot de la fin
Une petite doc, un tuto, qui je l'espère t’a aidé le mieux possible.
Je l'ai réalisé avec l'aide d'un menbre du groupe (https://www.facebook.com/groups/homeassistantgroupefrance)avec plaisir dans un esprit d’entre-aide, bénévolement, en parallèle de mon métier et ma vie.
Je n'assure pas le support de tous les problèmes mais je serais là si besoin sur le groupe, comme d’habitude.
Si tout fonctionne, remercie-moi.

Yann RITTER
