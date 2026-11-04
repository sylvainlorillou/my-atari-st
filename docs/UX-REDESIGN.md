# MY ATARI ST — refonte UX/UI

Statut : spécification et maquettes uniquement. Aucun changement de code applicatif dans ce commit.

Branche de travail : `hatari-wasm`. Ne pas fusionner vers `main` avant validation explicite.

## Décisions validées

- Bibliothèque de disquettes : panneau latéral escamotable (`side`).
- Écran : grand écran prioritaire (`large`).
- Navigation : onglets en haut (`tabs`).
- Fonctionnalités : animations, profils, restauration de session.
- Matériel visuel validé : boîte de disquettes V9, lecteurs A/B, joysticks noir/jaune et noir/rouge, souris Atari grise.
- Affectations clavier : Port 1 FIRE = ` (backtick); Port 2 FIRE = =. Interdire tout chevauchement de touches entre deux joysticks actifs.
- Souris : Trackpad par défaut sur Mac, ou souris externe, choix mémorisé.

## 1. Architecture de page

### Barre supérieure persistante
Logo MY ATARI ST à gauche. Onglets :
1. Console
2. Disquettes
3. Contrôles
4. Profils
5. Options

À droite : aide, état moteur, accès rapide aux préférences. L'onglet actif est signalé par un soulignement/liseré discret.

### Console — vue par défaut
Disposition desktop en trois zones :
- Zone centrale gauche (prioritaire, environ 60–65 %) : moniteur Atari, large et centré.
- Zone droite (environ 35–40 %) : lecteurs A et B côte à côte, puis cartes compactes de contrôle rapide.
- Bibliothèque : panneau latéral droit superposé/escamotable, qui s'ouvre sans redimensionner brutalement le moniteur. Sur petit écran, elle devient un tiroir plein écran.

Barre d'action fixe sous la console : Démarrer/Pause, Reset, Plein écran, état du moteur, volume si disponible.

### Disquettes
Vue bibliothèque dédiée : conserver sans altération le design de boîte V9 et sa géométrie validée. Recherche, catégories, sélection, informations de jeu et action « Insérer dans A » / « Insérer dans B ». Les lecteurs physiques virtuels A/B restent visibles dans la console. Une sélection ne monte jamais un disque sans action explicite, sauf jeux multi-disquettes dont le comportement automatique A/B est déjà prévu et validé.

### Contrôles
Deux cartes de joystick côte à côte, illustrations fidèles et panneaux gris neutre. Port 1 jaune, Port 2 rouge. Chaque carte présente touches directionnelles, FIRE, activation, disposition et états.
Carte Souris en dessous : Trackpad (choix initial sur Mac) ou Souris externe.
Validation des conflits avant sauvegarde/application : aucune touche ne peut être assignée simultanément à deux joysticks activés. Refuser la modification conflictuelle et indiquer précisément le port et la touche en cause. Ne pas réaffecter automatiquement l'autre joueur.
Réutiliser le comportement technique déjà validé, sans modifier le cœur de l'émulateur sauf nécessité prouvée.

### Profils
Trois profils initiaux :
- Jeux : réglages de contrôles adaptés aux jeux.
- Productivité : priorité à la souris et au clavier.
- Personnel : configuration libre.
Un profil est un instantané de préférences (joysticks, souris, affichage et options UI). Un changement de profil ne doit pas changer les disquettes montées.

### Session
Restauration de la dernière session : profil actif, préférences UI, périphériques et identifiants de disquettes déjà montées, lorsque les fichiers correspondants sont toujours disponibles dans la bibliothèque locale. Si un fichier a disparu, afficher un avertissement et laisser le lecteur vide ; ne jamais tenter de récupérer un fichier distant.
Ne pas sauvegarder l'état RAM de l'émulateur dans la première version.

## 2. Maquettes d'état

### Maquette A — Console standard (desktop)
- Barre d'onglets en haut.
- Moniteur large à gauche.
- À droite : lecteurs A/B en tête, cartes de contrôle rapide sous les lecteurs.
- Tiroir bibliothèque ouvert sur demande, au bord droit, avec la boîte V9 en liste verticale.
- Barre de commandes toujours visible.

### Maquette B — Contrôles
- Même barre supérieure.
- Deux joysticks sur une ligne, en gris, avec uniquement les couleurs réelles des boutons (jaune et rouge).
- Souris en carte pleine largeur sous les deux joysticks.
- Affectations et cases compactes, états lisibles.
- Alerte de conflit inline, sans boîte de dialogue bloquante si une simple correction suffit.

### Maquette C — Disquettes
- Boîte V9 comme élément central du catalogue.
- Détail de la disquette sélectionnée.
- Cibles A et B clairement visibles ; glisser-déposer possible en amélioration, boutons explicites indispensables.
- Pour un jeu à deux disquettes, proposer l'insertion automatique dans A et B.

### Maquette D — Profils et session
- Trois cartes de profils, profil actif évident.
- Actions : Activer, Enregistrer les changements, Dupliquer, Réinitialiser.
- État de session : dernière session restaurable, date locale, liste des lecteurs, profil.
- Confirmation uniquement avant remplacement d'un profil personnalisé.

### Maquette E — Mobile/tablette
- Onglets compacts ou barre de navigation inférieure.
- Moniteur en premier, commandes essentielles sous l'écran.
- Lecteurs A/B en grille 2 colonnes si la largeur le permet, sinon empilés.
- Bibliothèque en tiroir plein écran.
- Panneaux de contrôle empilés.

## 3. Animations et micro-interactions

- Ouverture/fermeture du tiroir : translation horizontale courte, 180–240 ms.
- Changement d'onglet : fondu léger, 120–160 ms, sans recharger l'émulateur.
- Insertion/éjection : animation brève de la disquette vers le lecteur, puis voyant d'activité.
- Voyant A/B : gris à vide, vert fixe lorsqu'un disque est monté, activité animée uniquement pendant lecture/écriture si le moteur expose réellement cet état.
- Sélection d'une disquette : mise en évidence de la tranche et déplacement de la boîte conformément au prototype V9.
- Respecter `prefers-reduced-motion` : animations désactivées ou réduites.

Aucune animation ne doit bloquer les commandes d'émulation ni retarder les événements clavier.

## 4. Modèle de données proposé

- `uiPreferences`: onglet initial, bibliothèque ouverte/fermée, densité, thème, animations.
- `profiles[profileId]`: réglages joystick 1/2, souris, options d'affichage.
- `activeProfileId`: profil sélectionné.
- `sessionSnapshot`: profil, lecteurs A/B, identifiants locaux des fichiers, date de sauvegarde.
- Les images de disquettes et leurs métadonnées restent dans l'IndexedDB existante (`myAtariShelf` / store `disks`) ; ne pas dupliquer les fichiers dans localStorage.

Prévoir une version de schéma et une migration douce des clés actuelles (`myAtariJoySettings`, `myAtariJoySettingsPort2`) vers le nouveau modèle. En cas d'erreur, conserver les anciennes données et revenir aux valeurs par défaut sûres.

## 5. Plan d'intégration — sans toucher à main

### Phase 0 — sécurisation
- Conserver `main` inchangée.
- Continuer sur `hatari-wasm`.
- Créer un point de retour/tag avant refonte.
- Vérifier les builds et le fonctionnement actuel de Hatari, des lecteurs et des périphériques.

### Phase 1 — structure et responsive
- Introduire le shell applicatif, les onglets et le système de panneaux.
- Garder l'instance émulateur montée en permanence lors du changement d'onglet (masquer/afficher son conteneur, ne pas détruire l'iframe ou le module).
- Intégrer les composants validés dans les nouveaux conteneurs.
- Tests desktop, tablette et mobile.

### Phase 2 — bibliothèque et lecteurs
- Réutiliser l'IndexedDB et les fonctions d'import/montage existantes.
- Replacer la boîte V9 dans le tiroir latéral.
- Relier les actions d'insertion aux lecteurs A/B réels.
- Tester jeux simples et jeux multi-disquettes.

### Phase 3 — contrôles
- Regrouper les réglages Port 1, Port 2 et souris dans l'onglet Contrôles.
- Ajouter le validateur de conflits partagé par les deux ports.
- Tester chaque disposition, chaque FIRE, activation/désactivation et persistance.
- Vérifier explicitement que Port 1 FIRE reste ` et Port 2 FIRE reste =.

### Phase 4 — profils et session
- Ajouter profils, sauvegarde et restauration de session.
- Migrer les anciennes préférences sans perte.
- Gérer les disquettes manquantes et les profils corrompus.

### Phase 5 — animations et finition
- Ajouter les animations discrètes, états des voyants et micro-interactions.
- Mesurer l'impact sur les performances.
- Vérifier le mode réduction des animations.

### Phase 6 — validation et intégration
- Recette fonctionnelle et visuelle avec l'utilisateur.
- Vérification des navigateurs ciblés, clavier AZERTY/QWERTY et Mac.
- Fusion vers `main` uniquement après accord explicite et vérification de la compatibilité avec la page principale et son moteur actuel.

## 6. Critères d'acceptation

- L'écran reste le point focal et conserve une taille confortable.
- La bibliothèque latérale s'ouvre/ferme sans redémarrer l'émulateur.
- A et B sont visibles dans la console.
- Le catalogue réutilise exactement le design de boîte V9.
- Les deux joysticks ne peuvent jamais partager une touche lorsqu'ils sont actifs.
- Port 1 FIRE = ` ; Port 2 FIRE = =.
- Trackpad est le choix initial sur Mac ; Souris externe est sélectionnable.
- Les profils et la session survivent à un rechargement.
- Les disquettes locales restent dans IndexedDB et ne sont pas téléversées.
- Les animations sont fluides, courtes et désactivables.
- Aucun changement n'est apporté à `main` avant validation.

## 7. Points explicitement hors périmètre de la première refonte

- Synchronisation cloud des disquettes ou profils.
- Sauvegarde/restauration complète de la RAM de l'émulateur.
- Détection automatique certaine trackpad vs souris USB : le navigateur ne fournit pas toujours cette distinction.
- Distribution de ROMs ou jeux commerciaux.
