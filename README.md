# 2023-2024 : Capteur low-tech Graphite (Tan Anh Khoa NGO &amp; Le Duc Minh TRAN)

## SOMMAIRE
*** 
  - [Contexte](#contexte)
  - [Livrables](#livrables)
  - [Matériel nécessaire](#matériel-nécessaire)
  - [1. Simulation électronique du capteur sous LTSpice](#simulation-électronique-du-capteur-sous-ltspice)
  - [2. Design du PCB sous KiCad](#design-du-pcb-sous-kicad)
  - [3. Code arduino](#code-arduino)
  - [4. Application android APK sous MIT App Inventor](#application-android-apk-sous-mit-app-inventor)
  - [5. Réalisation du shield](#réalisation-du-shield)
  - [6. Banc de test](#banc-de-test)
  - [7. Datasheet](#datasheet)
  - [Contact](#contact) 
  

*** 

## Contexte
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dans le cadre de l'unité de formation "Du capteur au banc de test" dispensée en 4ème année du cursus Génie Physique à l'INSA Toulouse, notre équipe a conçu un dispositif de mesure de contrainte innovant. L'inspiration pour le capteur de ce dispositif provient de l'article scientifique "Pencil Drawn Strain Gauges and Chemiresistors on Paper" (Cheng-Wei Lin*, Zhibo Zhao*, Jaemyung Kim & Jiaxing Huang). Ce capteur, d'une simplicité remarquable, est constitué d'un morceau de papier sur lequel du graphite est appliqué à l'aide d'un crayon à papier. \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Le principe de fonctionnement repose sur l'observation que la déformation du papier entraîne une variation du nombre de particules de graphite interconnectées. Cette modification de la structure granulaire du graphite se traduit par une variation de la résistance et de la conductance du capteur. En exploitant cette propriété, il devient possible de remonter à la déformation subie, à l'instar d'une jauge de contrainte classique.\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Notre objectif principal était de développer un dispositif de mesure de déformation basé sur un capteur low-tech. Pour atteindre cet objectif, nous avons suivi une démarche structurée comprenant plusieurs étapes clés : simulations électroniques pour valider le concept, conception du circuit imprimé (PCB) pour intégrer le capteur, développement du code Arduino pour l'acquisition et le traitement des données, et enfin, réalisation de la fiche technique du dispositif.

## Livrables
Les livrables de ce projet sont les suivants :
  
* Un shield PCB personnalisé connecté à une carte Arduino UNO. Ce shield intègre :
     
   - Un capteur de contrainte innovant à base de graphite.
   - Un amplificateur à transimpédance pour traiter le signal du capteur.
   - Un module Bluetooth pour la communication sans fil.
   - Un écran OLED pour l'affichage des données (ajouté pour améliorer l'expérience utilisateur).
   - Un capteur de flexion commercial pour comparaison (ajouté à des fins de validation).
   - Un potentiomètre numérique pour ajuster le gain de l'amplificateur (ajouté pour plus de flexibilité).
   - Un encodeur rotatif pour basculer entre les mesures des capteurs (ajouté pour faciliter la comparaison).
  
* Code Arduino     
   - L'acquisition des mesures de contrainte du capteur graphite.
   - La connexion et la communication Bluetooth.
   - L'affichage des données sur l'écran OLED.
   - Le réglage du gain via le potentiomètre numérique.
   - La sélection du capteur actif avec l'encodeur rotatif.
   - Réaliser des essais de banc de test rigoureux.
   - Évaluer les performances du capteur de contrainte graphite.
  
* Une application Android intuitive (APK) (développée avec MIT App Inventor) qui :
   - Permet de visualiser les données en temps réel.
   - Offre une interface conviviale pour interagir avec le système.

* Une datasheet complète qui détaille :
   - Les caractéristiques techniques du capteur graphite.
   - Son principe de fonctionnement.
   - Les instructions d'utilisation et de calibration.
   - Les résultats des tests de performance.

## Matériel nécessaire
Pour réaliser notre dispositif électronique, voici la liste des composants nécessaires :

- Résistances : 1 de 1 $k\ohm$, 1 de $10k\ohm$, 2 de $100k\ohm$ pour l'amplificateur transimpédance. 3 de $10k\ohm$ pour les boutons (peuvent être éviter en utilisant les résistances internes de l'arduino) et 1 de $33k\ohm$ pour le flex sensor.
- Capacités : 3 de 100 nF, 1 de 1 uF
- Arduino Uno
- Amplificateur opérationnel LTC1050
- Potentiomètre digital MCP41050
- Module Bluetooth HC05
- Ecran OLED 128x64
- Flex Sensor
- Encodeur rotatif KY-040


## Simulation électronique du capteur sous LTSpice
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Pour amplifier le signal de notre capteur de graphite, caractérisé par une résistance élevée et un courant de sortie faible, nous avons conçu un montage à transimpédance. Ce montage, basé sur un amplificateur opérationnel (AOP), permet de convertir le faible courant en une tension exploitable par le convertisseur analogique-numérique (ADC) de l'Arduino UNO. \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;La conception du circuit a été simulée sur LTspice, en suivant un [schéma spécifique](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/blob/main/Images/LTspice/schema_suggere.png). Le choix de l'AOP s'est porté sur le LTC 1050, reconnu pour sa capacité à gérer des courants d'entrée faibles et son faible offset de tension, garantissant ainsi la précision des mesures. Voici son schéma :

![capteur_graphite](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/blob/main/Images/LTspice/Schema_complet.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Afin d'optimiser la qualité du signal, trois filtres ont été intégrés au circuit :

- Un filtre passe-bas passif (R1, C1) à l'entrée, avec une fréquence de coupure de 16 Hz, pour atténuer les bruits en courant.
- Un filtre passe-bas actif (R3, C4) couplé à l'AOP, avec une fréquence de coupure de 1,6 Hz, pour filtrer le bruit à 50 Hz du réseau électrique.
- Un filtre passe-bas (R6, C2) à la sortie de l'AOP, avec une fréquence de coupure de 1,6 kHz, pour éliminer les bruits générés lors du traitement du signal.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;De plus, des composants supplémentaires ont été ajoutés pour améliorer la robustesse du circuit :
- La capacité C3 pour filtrer le bruit de l'alimentation.
- La résistance R5 pour protéger l'AOP contre les décharges électrostatiques et former un filtre passe-bas avec C1 pour les bruits en tension.
- La résistance R2, qui sera remplacée par un potentiomètre numérique pour ajuster le gain de l'AOP selon les besoins.

Les tests effectués sur le circuit simulé ont confirmé l'efficacité des filtres.


Voici la réponse de notre circuit pour vérfier que le capteur soit correctement amplifié : 

![Test_En_Transient](/Images/LTspice/1V-Capteur.png)
	
Le signal amplifié atteint 1V, une valeur compatible avec l'Arduino UNO.

De plus, la simulation d'un courant alternatif a démontré une atténuation significative du bruit, notamment une réduction d'environ 72 dB à 50 Hz.

![Test_En_AC](/Images/LTspice/AC-Capteur.png)



## Design du PCB sous KiCad



## Code arduino



## Application android APK sous MIT App Inventor
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Une application est également disponible avec le dispositif : développé sous [MIT App Inventor](https://appinventor.mit.edu/) . 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;L'application reçoit les données transmis par le module bluetooth HC-05. Elle affiche ensuite la valeur tranmis (des $M\ohm$ pour le capteur graphite ou des $\ohm$ pour le flex sensor.

<img src="/Images/Appli_Android.jpg" alt="Android" style="width:500px;height:1000px;">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;L'application peut s'installer [sous android ici](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/tree/main/Android/CapteurGraphite.apk) et toutes les informations liées à l'application son dans [le dossier Application Android](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/tree/main/Android)

## Réalisation du shield




## Banc de test

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Pour spécifier notre capteur et son montage transimpédance, nous avons décidé d'utiliser des demis-cercles avec un rayon différents.

![Image Banc de Test](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/blob/main/Images/Banc_de_test-Pyramide.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ce sont des demis-cercles, imprimés en 3D, avec un diamètre commençant à 2 cm pour le plus petit et en augmentant de 0.5 cm par créneau. Le plus grand demi-cercle a un diamètre de 5cm. Grâce à ces demis-cercles, nous allons pouvoir calculer la variation de la résistance électrique $\frac{\Delta R}{R_0}$ en fonction de la déformation $\epsilon=\frac{e}{D}$. Nous avons ici mesuré notre épaisseur de notre papier $e=0.2 mm$. Ainsi, nous avons une déformation variant de 0.1 pour le plus petit diamètre à 0.04 pour le plus grand. Voici les courbes caractéristiques pour des crayons 2B, B et HB pour [des tensions ou des compressions.](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/tree/main/Images/Compression-Tension.png)

![Compression](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/blob/main/Images/Compression.png)

![Tension](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/blob/main/Images/Tension.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;On remarque que la résistance augmente lorsque l'on met le capteur en tension et qu'elle diminue lors de la compression de ce dernier. En tension, la distance entre les atomes de carbones augmente et la résistance augmente avec. Le contraire se produit pour la compression. \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;En fonction de la dureté du crayon utilisé, les variations relatives de résistance changent. Plus le crayon est gras (2H->H->HB->B->2B avec 2B avec le plus de carbone), moins sa variation relative de résistance est élevée.

## Datasheet

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Vous pouvez retrouver la datasheet du produit [ici](https://github.com/MOSH-Insa-Toulouse/2023-2024_4GP_NGO-TRAN/tree/main/datasheet.pdf)


## Contact

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Vous pouvez nous contacter pour toutes questions :
- Tan Anh Khoa NGO : takngo@insa-toulouse.fr
- Le Duc Minh TRAN : ltra@insa-toulouse.fr
