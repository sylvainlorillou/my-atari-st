# MY ATARI ST — prototype 0.1

## Lancer
- Ouvrir `index.html` dans un navigateur récent.
- Pour une meilleure compatibilité, servir le dossier avec un petit serveur local :
  `python3 -m http.server 8000`
  puis ouvrir http://localhost:8000

## Ce qui fonctionne dans ce prototype
- Interface hybride : bibliothèque + bureau Atari intégré.
- Emulateur EstyJS 2.0 chargé dans le bureau.
- Bascule de filtre CRT.
- Mode plein écran et redémarrage du moteur.
- Bibliothèque locale (localStorage) : ajout/suppression/recherche d'entrées.
- Commandes joystick EstyJS : flèches + CTRL (après clic dans l'émulateur).

## Limites connues
- Le moteur est hébergé par EstyJS et nécessite une connexion Internet.
- Pour charger réellement une disquette, utiliser le contrôle DISK A natif dans l'interface EstyJS. Le formulaire de bibliothèque ne téléverse pas le fichier dans l'émulateur : il mémorise seulement son nom et une entrée locale.
- EstyJS documente le support des images `.st` et `.sts`. Les autres extensions proposées dans le formulaire ne sont pas garanties par le moteur.
- Pas de ROM commerciale fournie. Utiliser des images de disquettes dont vous possédez les droits.
- EstyJS est une émulation ST orientée jeux/démonstration, pas une émulation STE complète. Pour une meilleure fidélité STE, l'étape suivante sera une intégration Hatari WebAssembly.

## Sources / licences
- EstyJS 2.0 : https://kaiec.github.io/EstyJS/
- Code source : https://github.com/kaiec/EstyJS
- Hatari (piste future) : https://github.com/hatari/hatari
