# EasyCallSheet – Documentation Interface & Design UX

Ce document décrit en détail le design et le fonctionnement de l’interface EasyCallSheet. Il sert de **référence réutilisable** pour maintenir ou faire évoluer l’application dans un style cohérent.

---

## 1. Philosophie du design

### 1.1 Principes directeurs

- **Simplicité** : une seule page, formulaire à gauche et aperçu à droite.
- **Clarté** : sections du formulaire bien délimitées (Format et Date, Informations, Contact), libellés explicites.
- **Efficacité** : listes réutilisables (formats, responsables) avec autocomplétion et import/export CSV pour éviter la ressaisie.
- **Feedback** : aperçu en direct, message de chargement pendant la génération PDF, modales pour la gestion des listes.

### 1.2 Ton visuel

- Professionnel, sobre, adapté à un usage interne (équipes L’Étudiant).
- Thème clair par défaut (fond gris clair, cartes blanches).
- Police : **Nunito** (corps et titres).

---

## 2. Palette de couleurs

### 2.1 Couleurs principales

| Couleur | Usage | Valeur (hex) |
|---------|-------|--------------|
| **Bleu principal** | Titre, boutons principaux, liens, focus, accents dans les listes | `#007aff` |
| **Bleu survol** | Boutons au survol | `#0055b3` |
| **Fond page** | Arrière-plan global | `#f5f5f7` |
| **Fond formulaire** | Panneau gauche | `#f9f9f9` |
| **Fond contenu** | Cartes, zone aperçu, modales | `#ffffff` / `#fefefe` |
| **Texte principal** | Corps de texte | `#333` |
| **Texte secondaire** | Labels, sous-titres | `#555`, `#666` |
| **Bordures** | Champs, séparateurs | `#ddd`, `#e0e0e0` |
| **Danger** | Bouton Supprimer | `#dc3545` (hover `#c82333`) |
| **Secondaire** | Boutons « Gérer… », secondaires | `#6c757d` (hover `#5a6268`) |
| **Logo / marque** | Texte logo dans l’aperçu PDF | `#274190` |

### 2.2 Focus et états

- **Focus champs** : `border-color: #007aff`, `box-shadow: 0 0 0 2px rgba(0, 122, 255, 0.2)`.
- **Survol boutons** : changement de teinte (bleu plus foncé, gris plus foncé).
- Pas de thème sombre défini dans l’état actuel ; l’interface est en thème clair uniquement.

---

## 3. Typographie

### 3.1 Police

```css
font-family: 'Nunito', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
```

- **Nunito** : chargée via Google Fonts ; utilisée pour tout le texte (formulaire, aperçu, modales, fenêtre d’impression).

### 3.2 Hiérarchie

| Élément | Contexte | Taille | Poids |
|---------|----------|--------|-------|
| Titre page | `h1` | défaut | couleur `#007aff` |
| Titre de section formulaire | `h2` dans `.form-group` | 1.2rem | bordure basse, padding-bottom |
| Titre modale | `.modal-header h2` | défaut | sans bordure |
| Sous-titres modale | `h3` dans `.modal-body` | 1.1rem | couleur `#555` |
| Labels | `label` | défaut | font-weight 500, couleur `#555` |
| Corps | défaut | 14px (inputs), 1.6 line-height | 400 |
| Aperçu PDF | `.pdf-content`, `.pdf-section h3` | 16px titres section | bold |
| Logo texte aperçu | `.pdf-logo-text` | 28px | bold, `#274190` |

---

## 4. Structure de la mise en page

### 4.1 Conteneur global

- **`.container`** : `max-width: 1200px`, `margin: 0 auto`, `padding: 20px`.

### 4.2 Zone principale (`.app-container`)

- **Layout** : `display: flex` ; sur grand écran : formulaire à gauche, aperçu à droite.
- **Style** : fond blanc, `border-radius: 10px`, `box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1)`, `min-height: 80vh`.

### 4.3 Section formulaire (`.form-section`)

- **Largeur** : `flex: 0 0 350px`, padding 20px, fond `#f9f9f9`, bordure droite `1px solid #ddd`.
- **Défilement** : `overflow-y: auto` si contenu long.
- **Groupes** : `.form-group` (margin-bottom 25px), `.form-item` (margin-bottom 15px).

### 4.4 Section aperçu (`.preview-section`)

- **Flex** : `flex: 1`, padding 30px, `overflow-y: auto`.
- **Contenu** : `#previewContent` avec `max-width: 800px`, margin auto, padding 20px, fond blanc, légère ombre.

### 4.5 Responsive (max-width: 768px)

- `.app-container` en `flex-direction: column`.
- `.form-section` en pleine largeur, bordure droite remplacée par bordure basse.
- Modales en `width: 95%` ; listes (managers/formats) en colonne avec boutons Modifier/Supprimer en ligne en dessous.

---

## 5. Composants réutilisables

### 5.1 Boutons

**Boutons principaux (primaire)**  
- Fond `#007aff`, texte blanc, padding 10px 20px, `border-radius: 5px`, `font-weight: 500`.  
- Survol : `#0055b3`.  
- Utilisation : « Aperçu », « Générer PDF », « Ajouter » dans les modales.

**Boutons secondaires (`.secondary-button`)**  
- Fond `#6c757d`, `font-size: 13px`, padding 8px 15px.  
- Survol : `#5a6268`.  
- Utilisation : « Gérer les formats », « Gérer les responsables… », « Exporter en CSV ».

**Boutons d’action dans les listes**  
- **Modifier** (`.edit-button`) : bleu `#007aff`, petit (6px 12px, 12px font).  
- **Supprimer** (`.delete-button`) : rouge `#dc3545`, même taille.

### 5.2 Champs de formulaire

- **Input / select / textarea** : `width: 100%`, padding 10px, `border: 1px solid #ddd`, `border-radius: 5px`, `font-size: 14px`.
- **Focus** : `outline: none`, `border-color: #007aff`, `box-shadow: 0 0 0 2px rgba(0, 122, 255, 0.2)`.
- **Labels** : `display: block`, `margin-bottom: 5px`, `font-weight: 500`, `color: #555`.
- **Textarea** : `resize: vertical`, `min-height: 60px`.

### 5.3 Cases à cocher

- **`.checkbox-label`** : `display: flex`, `align-items: center`, `cursor: pointer`, `font-weight: normal`.
- Checkbox : `width: auto`, `margin-right: 8px`.

### 5.4 Groupe de boutons principal

- **`.button-group`** : `display: flex`, `justify-content: space-between`, `margin-top: 30px` (Aperçu + Générer PDF).

---

## 6. Autocomplétion

- **Conteneur** : `.autocomplete-wrapper` en `position: relative`.
- **Liste** : `.autocomplete-suggestions` — position absolue sous le champ, fond blanc, bordure, `max-height: 200px`, `overflow-y: auto`, `z-index: 1000`, `box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1)`.
- **Élément** : `.suggestion-item` — padding 10px, survol `background-color: #f5f5f5`, `border-bottom: 1px solid #f0f0f0`.
- Utilisée pour « Responsable projet » et « Responsable vidéo » ; au clic, remplissage du nom et du téléphone.

---

## 7. Modales

### 7.1 Structure

```html
<div id="…" class="modal">
  <div class="modal-content">
    <div class="modal-header">
      <h2>Titre</h2>
      <span class="modal-close">&times;</span>
    </div>
    <div class="modal-body">...</div>
  </div>
</div>
```

### 7.2 Style

- **Overlay** (`.modal`) : `position: fixed`, plein écran, `background-color: rgba(0, 0, 0, 0.5)`, `z-index: 10000`, `display: none` (affiché en `block` à l’ouverture).
- **Contenu** (`.modal-content`) : `max-width: 600px`, `width: 90%`, margin 5% auto, fond `#fefefe`, `border-radius: 10px`, `box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3)`.
- **Header** : flex entre titre et croix, padding 20px, `border-bottom: 1px solid #ddd`, fond `#f9f9f9`, coins supérieurs arrondis.
- **Corps** : padding 20px.
- **Fermeture** : `.modal-close` — couleur `#aaa`, font-size 28px ; survol `#000`. Clic sur l’overlay ferme aussi la modale.

### 7.3 Contenu des modales

- **Responsables (projet / vidéo)** : formulaire d’ajout (nom, téléphone) → bouton « Ajouter » / « Sauvegarder » en édition ; import CSV (nom,téléphone) ; liste avec Modifier / Supprimer ; export CSV.
- **Formats** : formulaire d’ajout (nom) ; import CSV (nom) ; liste avec Modifier / Supprimer ; export CSV.
- Séparations visuelles : `.add-manager-form`, `.import-csv-form` avec `border-bottom: 2px solid #ddd` et marges/padding.

---

## 8. Listes dans les modales (managers / formats)

- **Liste** : `ul` sans puces, `.managers-list li` (ou équivalent pour formats) en flex entre infos et actions.
- **Ligne** : padding 12px, fond `#f9f9f9`, `border-radius: 5px`, `border: 1px solid #e0e0e0` ; survol `#f0f0f0`.
- **Infos** : `.manager-info` avec `strong` en `#007aff` pour le nom.
- **Actions** : `.manager-actions` en flex, gap 8px (Modifier, Supprimer).
- **Message vide** : `.empty-message` — texte centré, couleur `#999`, italique, padding 20px.

---

## 9. Aperçu et contenu PDF

- **Conteneur** : `.pdf-content` dans `#previewContent` (et dans la fenêtre d’impression).
- **En-tête** : `.pdf-header` — texte centré, `.pdf-logo-text` (L’Étudiant) en 28px bold `#274190`, titre « FEUILLE DE SERVICE - [DATE] ».
- **Sections** : `.pdf-section` — `margin-bottom: 25px` ; `h3` en 16px bold.
- **Impression** : dans la fenêtre d’impression, logo via `logo_etudiant.png` ; styles inline pour A4, marges 20mm ; `page-break-inside: avoid` sur les sections.

---

## 10. Message de chargement (génération PDF)

- Élément temporaire : position fixed, centré (top 50%, left 50%, transform translate -50% -50%), padding 20px, fond `rgba(0,0,0,0.7)`, texte blanc, `border-radius: 5px`, `z-index: 9999`.
- Texte : « Génération du PDF en cours… ». Supprimé du DOM à la fin (succès ou erreur).

---

## 11. Rayons de bordure et ombres

| Usage | Valeur |
|-------|--------|
| Champs, boutons | `border-radius: 5px` |
| Conteneur principal, modales | `border-radius: 10px` |
| Ombre conteneur | `box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1)` |
| Ombre modale | `box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3)` |
| Ombre suggestions | `box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1)` |

---

## 12. Transitions

- Boutons : `transition: background-color 0.2s` pour le changement de couleur au survol.
- Pas d’animations complexes sur les modales (affichage/masquage en `display`).

---

## 13. Règles UX

### 13.1 Feedback

- **Aperçu** : mis à jour au clic sur « Aperçu » (pas de mise à jour en temps réel à chaque frappe dans l’état actuel).
- **Génération PDF** : message de chargement visible pendant l’ouverture de la fenêtre et la préparation de l’impression.
- **Erreur** : alertes pour champs obligatoires manquants, fichier CSV manquant ou invalide, doublons (formats), confirmation avant suppression.

### 13.2 Champs conditionnels

- « Aménager les horaires » : affiche/masque les champs Installation, HMC Début/Fin, Heure RDV, Fin de tournage.
- « Tournage en extérieur » : affiche/masque le champ Adresse du tournage.

### 13.3 Sauvegarde automatique

- Lors de la génération PDF, les responsables projet et vidéo saisis (s’ils ne figurent pas déjà dans les listes) sont ajoutés automatiquement aux listes (autoSaveManager, autoSaveVideoManager).

### 13.4 Accessibilité

- Labels associés aux champs (for/id).
- Contraste suffisant (texte sombre sur fond clair).
- Champs focus visibles (bordure et halo bleu).

---

## 14. Checklist pour cohérence des évolutions

- [ ] Conserver la police Nunito et la palette (bleu `#007aff`, gris, rouge pour supprimer).
- [ ] Garder la structure `.container` > `.app-container` > `.form-section` + `.preview-section`.
- [ ] Utiliser `.primary-button` / `.secondary-button` et `.edit-button` / `.delete-button` pour les actions.
- [ ] Réutiliser le pattern modal (`.modal` > `.modal-content` > `.modal-header` + `.modal-body`).
- [ ] Respecter les mêmes `border-radius` et ombres pour les cartes et modales.
- [ ] En ajoutant des écrans ou des vues, privilégier le même langage visuel (sections avec h2, form-item, etc.).

---

*Document de référence pour la maintenance et l’évolution de l’interface EasyCallSheet.*
