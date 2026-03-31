# EasyCallSheet – Document récapitulatif pour l'amélioration de l'application

Ce document décrit l'application EasyCallSheet afin qu'un assistant (ex. Claude) puisse proposer des évolutions cohérentes avec l'existant.

---

## 1. Vue d'ensemble

**EasyCallSheet** est une **application web** (front uniquement) qui permet de générer des **feuilles de service** (call sheets) pour les tournages vidéo de **L'Étudiant** :

- Saisie des informations de tournage : format, date, invité, école, horaires (PAT, optionnellement personnalisés), lieu (intérieur / extérieur avec adresse), contacts.
- Gestion de listes réutilisables : **formats vidéo**, **responsables projet**, **responsables vidéo** (avec autocomplétion, import/export CSV).
- **Aperçu** en direct du document dans la page.
- **Génération PDF** : ouverture d’une fenêtre d’impression avec le contenu formaté (impression navigateur ou « Enregistrer en PDF »).
- **Pré-remplissage depuis l’URL** : paramètres issus de Monday.com (titre, format, date, heure, responsable, téléphone, lieu) pour pré-remplir le formulaire.

**Contexte métier** : outil interne pour préparer les feuilles de service des tournages (L’Étudiant, Carré Daumesnil ou extérieur), avec déroulé de journée (installation, RDV, HMC, tournage, fin) et notes aux intervenants.

**Lancement** : ouvrir `EasyCallSheets.html` dans un navigateur (ou servir le dossier via un serveur HTTP si besoin pour les ressources). Aucun build ni Node.js requis.

---

## 2. Stack technique

- **Type** : application web statique (HTML, CSS, JavaScript).
- **Fichiers principaux** : `EasyCallSheets.html`, `styles.css`, `script.js`.
- **Dépendances externes** :
  - Google Fonts : **Nunito** (police principale).
  - CDN : `html2pdf.js` (chargé dans la page ; la génération PDF actuelle utilise en pratique une fenêtre d’impression `window.open` + `window.print`).
- **Stockage** : **localStorage** du navigateur.
  - `easyCallSheets_managers` : liste des responsables projet (nom, téléphone).
  - `easyCallSheets_videoManagers` : liste des responsables vidéo (nom, téléphone).
  - `easyCallSheets_formats` : liste des formats (nom, durée par défaut 90).
- **Ressources** : image `logo_etudiant.png` (logo L’Étudiant, utilisée dans la fenêtre d’impression).

---

## 3. Architecture

| Rôle | Fichier(s) | Rôle |
|------|------------|------|
| **Structure / contenu** | `EasyCallSheets.html` | Formulaire (sections Format/Date, Infos, Contact), zone d’aperçu, modales (responsables projet, responsables vidéo, formats). |
| **Styles** | `styles.css` | Mise en page (formulaire / aperçu), composants (boutons, champs, modales, listes, autocomplétion), responsive, styles d’impression. |
| **Logique** | `script.js` | Gestion du formulaire, calcul des horaires, génération de l’aperçu et du PDF, gestion des listes (CRUD, import/export CSV), paramètres URL, autocomplétion. |

Une seule page ; pas de routage ni de processus séparés. Tout s’exécute dans le navigateur.

---

## 4. Structure des dossiers (source)

```
EasyCallSheet-main/
├── README.md
├── EASYCALLSHEET_DOCUMENTATION_APPLICATION.md   # Ce document
├── EASYCALLSHEET_DOCUMENTATION_INTERFACE_UX.md  # Design et UX
├── EasyCallSheets.html
├── styles.css
├── script.js
└── logo_etudiant.png
```

---

## 5. Parcours utilisateur et écrans

- **Écran principal** : une seule vue avec deux zones côte à côte (ou empilées sur mobile).
  - **Gauche** : formulaire en sections (Format et Date, Informations, Contact) + boutons « Aperçu » et « Générer PDF ».
  - **Droite** : zone d’aperçu (`#previewContent`) affichant le rendu de la feuille de service.

- **Modales** (overlay, pas de changement d’écran) :
  1. **Gérer les responsables projet** : ajout / édition / suppression, import CSV (nom,téléphone), export CSV.
  2. **Gérer les responsables vidéo** : idem.
  3. **Gérer les formats** : ajout / édition / suppression, import CSV (nom), export CSV.

- **Comportements** :
  - « Aménager les horaires » affiche les champs : Installation, HMC Début, HMC Fin, Heure RDV, Fin de tournage.
  - « Tournage en extérieur » affiche le champ Adresse du tournage.
  - Aperçu : mise à jour du bloc droit à partir des valeurs du formulaire.
  - Générer PDF : validation des champs obligatoires, message « Génération du PDF en cours… », ouverture d’une nouvelle fenêtre avec le contenu formaté puis appel à `window.print()` (l’utilisateur peut enregistrer en PDF via le dialogue d’impression).

**Pas de splash** ni de header type « barre d’outils » ; titre « EasyCallSheets » en haut de la page.

---

## 6. Données et état (côté client)

| Donnée | Rôle |
|--------|------|
| **Formulaire** | Champs contrôlés par les éléments du DOM (format, date, invité, école, heure PAT, cases à cocher, horaires personnalisés, adresse extérieur, responsable projet/vidéo, téléphones). |
| **Managers** | Liste chargée/sauvegardée via `getManagers()` / `saveManagers()` (localStorage). |
| **VideoManagers** | Liste chargée/sauvegardée via `getVideoManagers()` / `saveVideoManagers()` (localStorage). |
| **Formats** | Liste chargée/sauvegardée via `getFormats()` / `saveFormats()` (localStorage) ; initialisation avec `DEFAULT_FORMATS` si vide. |
| **Édition en cours** | `editingManagerIndex`, `editingVideoManagerIndex`, `editingFormatIndex` pour savoir si une modale est en mode « Modifier » (bouton « Ajouter » devient « Sauvegarder »). |

Pas de framework d’état ; l’état est implicite dans le DOM et les listes en mémoire synchronisées avec le localStorage.

---

## 7. Paramètres URL (intégration Monday)

Pour pré-remplir le formulaire depuis un lien (ex. depuis Monday.com), les paramètres suivants sont lus au chargement (`loadFromUrlParams()`) :

| Paramètre | Rôle |
|-----------|------|
| `titre` | Nom de la ligne Monday ; utilisé pour extraire **nom de l’invité** et **école** via `extractGuestAndSchool()` (patterns du type "Nom - École", "Interview Nom École", etc.). |
| `format` | Valeur du format ; mappée vers les options du select (ex. "Interview" → "L'interview"). |
| `date` | Date de tournage (YYYY-MM-DD ou DD/MM/YYYY). |
| `heure` | Heure PAT (HH:mm). |
| `responsable` | Nom du responsable projet ; remplit le champ et peut déclencher la récupération du téléphone depuis les responsables sauvegardés. |
| `telephone` | Téléphone du responsable projet. |
| `lieu` | Si présent, coche « Tournage en extérieur » et remplit l’adresse. |

---

## 8. Modules et fonctions principales (script.js)

### 8.1 Données et listes

- **Responsables projet** : `getManagers()`, `saveManagers()`, `addManager()`, `editManager()`, `deleteManager()`, `refreshManagersList()`.
- **Responsables vidéo** : `getVideoManagers()`, `saveVideoManagers()`, `addVideoManager()`, `editVideoManager()`, `deleteVideoManager()`, `refreshVideoManagersList()`.
- **Formats** : `getFormats()`, `saveFormats()`, `addFormat()`, `editFormat()`, `deleteFormat()`, `refreshFormatsList()`, `refreshFormatSelect()`.

### 8.2 Formulaire et affichage

- **Horaires** : `calculateSchedule(patTime, isCustom, customTimes)` — calcule installation, RDV, HMC, fin à partir de l’heure PAT ou des horaires personnalisés.
- **Aperçu** : `generateScheduleText()`, `generatePreview()` — construisent le HTML de l’aperçu (sections format, invité, lieu, RDV, contacts, déroulé, notes).
- **PDF** : `generatePDF()` — validation, construction du HTML d’impression (avec logo, styles inline), ouverture dans une nouvelle fenêtre, appel à `window.print()` après chargement du logo (ou timeout).

### 8.3 URL et autocomplétion

- **URL** : `extractGuestAndSchool(title)`, `loadFromUrlParams()`.
- **Autocomplétion** : `showSuggestions()` (responsable projet), `showVideoManagerSuggestions()` (responsable vidéo) ; remplissage du nom et du téléphone au clic sur une suggestion.

### 8.4 Import / export CSV

- Responsables : import (nom,téléphone), export.
- Responsables vidéo : idem.
- Formats : import (nom ou header « name »/« nom »/« format » ignoré), export (colonne `name`).

---

## 9. Règles métier et validation

- **Champs obligatoires pour la génération PDF** : date, nom de l’invité, heure PAT, responsable projet (nom + téléphone), responsable vidéo (nom + téléphone). Une alerte demande de les remplir si manquants.
- **Horaires par défaut** (si « Aménager les horaires » non coché) : installation = PAT − 30 min, RDV = PAT − 15 min, HMC = RDV, fin = PAT + 1h30.
- **Lieu** : si pas extérieur ou pas d’adresse, le texte par défaut est « L'Etudiant, Carré Daumesnil » et l’adresse 52, rue Jacques-Hillairet - 75012 PARIS.
- **Doublons** : ajout d’un responsable (projet ou vidéo) avec un nom déjà existant propose la mise à jour du numéro ; les formats en doublon (même nom) sont refusés à l’ajout.

---

## 10. Sécurité et limites

- Données stockées uniquement dans le **localStorage** du navigateur (pas de serveur).
- Aucune donnée sensible n’est envoyée à un serveur par l’application (hors éventuels CDN pour polices et scripts).
- Les paramètres URL sont lus et décodés côté client ; pas de sanitisation serveur (application 100 % client).

---

## 11. Évolutions possibles (pistes)

- Unifier la génération PDF (utiliser réellement html2pdf.js pour un fichier téléchargé, ou documenter clairement le flux « impression → Enregistrer en PDF »).
- Corriger la cohérence des IDs pour les horaires personnalisés (ex. `customHmcStart` / `customHmcEnd` vs `customHmc` dans `calculateSchedule` / `customTimes`) si une plage HMC distincte est souhaitée.
- Ajouter une version ou un numéro de build affiché dans l’interface.
- Option de thème (clair/sombre) et variables CSS pour rester aligné avec d’autres apps (ex. BackupFlow).

---

*Document à mettre à jour lorsque l’architecture ou les fonctionnalités évoluent.*
