# MY ATARI ST — prototype interface v0.2

- Interface inspirée du 1040 STE et du moniteur couleur SC1224 de la photo de référence.
- Bibliothèque simplifiée et import local des disquettes.
- Fichiers conservés dans IndexedDB du navigateur, sans téléversement.
- Écran de jeu unifié, sans interface EstyJS.

## Limite importante
Cette version sert à tester l'apparence et le parcours utilisateur. Elle ne lance pas encore réellement les disquettes : le moteur d'émulation doit être intégré dans l'écran de jeu. Le candidat étudié est NeoST 0.6.2, qui publie un build WebAssembly et annonce le support ST/STE, EmuTOS et des disquettes ST/MSA/STX/DIM/IPF. Intégration et tests navigateur restent à faire.

Moteur envisagé : https://github.com/habib256/neost
Release : https://github.com/habib256/neost/releases/tag/0.6.2
Licence : GPL-3.0. Vérifier les obligations de redistribution avant intégration.

Aucune ROM Atari commerciale ni jeu commercial n'est fourni.
