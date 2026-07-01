# Audit SEO — harang-facade.com
**Date :** 2026-07-01 | **Auditeur :** Claude Code (Lya)
**Périmètre :** Site vitrine statique HTML/CSS · Vercel · SEO local Ardennes (08)

---

## Synthèse exécutive

Le site dispose d'une base technique solide (HTTPS, `lang="fr"`, sitemap, robots.txt, JSON-LD HousePainter, formulaire Formspree) mais présente **trois blocages critiques** pour le référencement local : absence totale de mesure (GA4 + Search Console), H1 sans mot-clé géographique, et une incohérence NAP entre le JSON-LD et le HTML visible de la section Contact. Les images sont lourdes et sans format WebP, ce qui pénalise le LCP. Le contenu sous-représente la zone d'intervention (7 communes seulement, toutes en "A") et omet complètement le service **vitrerie**. La mise en place de GA4 + Search Console et la correction de l'H1 sont les deux actions à prioriser en priorité absolue avant toute optimisation de contenu.

---

## 1. Tableau des constats

### 🔴 Haute gravité

| # | Constat | Impact | Recommandation |
|---|---------|--------|---------------|
| H1 | **H1 sans mot-clé local** — `"Votre façade, impeccable."` ne contient aucune mention de Charleville-Mézières, Ardennes ni du métier | SEO local bloqué — Google ne peut pas scorer la page sur les requêtes géolocalisées | Réécrire le H1 : *"Peintre façadier à Charleville-Mézières — Ardennes"* ou variante (voir §3) |
| H2 | **Absence totale de GA4 et Google Search Console** | Aucune donnée de trafic, aucune visibilité sur les positions/clics, impossible de mesurer l'impact des optimisations | Installer GA4 + soumettre sitemap à la Search Console (voir §5) |
| H3 | **Pas de balise `<link rel="canonical">`** | Vercel expose potentiellement `www.harang-facade.com`, `harang-facade.com/`, et `https` vs `http` comme URLs distinctes → duplicate content | Ajouter `<link rel="canonical" href="https://harang-facade.com/" />` dans `<head>` |
| H4 | **NAP incohérente : numéro manquant dans le HTML visible** | JSON-LD : *"9 Rue Albert Poulain"* ✅ · Section Contact HTML : *"Rue Albert Poulain"* ❌ (sans le "9") → incohérence détectée par Google et les agrégateurs de citations locales | Corriger le HTML ligne 436 : ajouter le "9" |
| H5 | **Images sans attributs `width`/`height`** | CLS (Cumulative Layout Shift) non maîtrisé → Core Web Vital potentiellement rouge → pénalité ranking | Ajouter les dimensions réelles sur chaque `<img>` (voir §6) |

### 🟠 Moyenne gravité

| # | Constat | Impact | Recommandation |
|---|---------|--------|---------------|
| M1 | **Title trop long** — 69 caractères (max : 60) `"Harang Vincent — Peintre & Façadier \| Charleville-Mézières, Ardennes"` | Tronqué dans les SERP → perte de clic | Raccourcir : *"Peintre Façadier Charleville-Mézières \| Harang"* (55 car.) |
| M2 | **Meta description trop longue** — ~185 caractères (max recommandé : 155) | Tronquée dans les SERP | Raccourcir à 150-155 car. en conservant les mots-clés et le CTA "Devis gratuit" |
| M3 | **Images au format JPG, pas de WebP** — tailles : 305 KB (hero), 341 KB, 401 KB (la + lourde), 182 KB, 143 KB | LCP impacté (hero 305 KB non compressé), bande passante mobile | Convertir en WebP (économie ~60-70 % par image), servir via `<picture>` ou via Vercel Image Optimization |
| M4 | **Noms de fichiers images non descriptifs** — `1000028623.jpg`, `1000028624.jpg`… | Zéro signal SEO pour Google Images + difficile à maintenir | Renommer : `ravalement-facade-villers-semeuse-apres.jpg`, `renovation-cuisine-charleville.jpg`, etc. |
| M5 | **Pas de balise `<link rel="preload">` pour l'image hero (LCP)** | La plus grande image "above the fold" est découverte tardivement par le browser | Ajouter `<link rel="preload" as="image" href="img/[hero].webp" />` dans `<head>` |
| M6 | **Google Fonts chargées en render-blocking** — Inter + Montserrat via `fonts.googleapis.com` | Délai d'affichage du texte (FOUT), TTFB augmenté | Passer en `font-display: optional` ou auto-héberger les fontes (Fontsource npm ou téléchargement direct) |
| M7 | **Pas de balises Open Graph** | Partage sur réseaux sociaux sans image ni titre contrôlé → mauvaise présentation de la marque | Ajouter `og:title`, `og:description`, `og:image`, `og:url`, `og:type` |
| M8 | **Service "vitrerie" absent du contenu** | Requêtes "vitrier Charleville-Mézières" non capturées | Ajouter vitrerie dans la liste des prestations extérieures ou en prestation à part |
| M9 | **Zone d'intervention très limitée** — 7 communes listées (Villers-Semeuse, Charleville-Mézières + 5 communes en "A" seulement) | Opportunités locales manquées (Sedan, Rethel, Revin, Givet, Bogny-sur-Meuse…) | Compléter la liste avec les 15-20 communes où le client intervient réellement |
| M10 | **H2 sans mots-clés locaux** — "Ce que nous faisons", "Quelques chantiers récents"… | Signal SEO local sous-exploité | Enrichir : *"Nos travaux de peinture et façade dans les Ardennes"*, *"Chantiers récents à Charleville-Mézières et Villers-Semeuse"* |

### 🟡 Basse gravité

| # | Constat | Impact | Recommandation |
|---|---------|--------|---------------|
| B1 | **JSON-LD — champ `geo` (latitude/longitude) absent** | Signal de géolocalisation non transmis à Google | Ajouter `"geo": {"@type": "GeoCoordinates", "latitude": 49.747, "longitude": 4.723}` (coordonnées Villers-Semeuse) |
| B2 | **JSON-LD — champ `sameAs` absent** | Google ne peut pas relier la fiche au profil Google Business | Ajouter `"sameAs": ["https://maps.google.com/?cid=VOTRE_CID", "..."]` |
| B3 | **JSON-LD — champ `description` absent** | Moins de contexte sémantique pour les moteurs | Ajouter une description courte du profil de l'artisan |
| B4 | **JSON-LD — `areaServed` générique** (3 strings) | Peu exploitable pour les rich results locaux | Remplacer par des objets `{"@type": "City", "name": "Charleville-Mézières"}` pour chaque commune |
| B5 | **Sitemap.xml — `<lastmod>` absent** | Google ne sait pas quand la page a été modifiée en dernier | Ajouter `<lastmod>2026-07-01</lastmod>` et mettre à jour à chaque déploiement |
| B6 | **Pas d'embed Google Maps** | Manque un signal de localisation visuel + texte ancre | Intégrer une `<iframe>` Google Maps dans la section Zone ou Contact |
| B7 | **Favicon uniquement SVG inline data URI** | Pas d'`apple-touch-icon` PNG → mauvais rendu sur iOS/Safari | Ajouter `<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png" />` |
| B8 | **Pas de Twitter Card** | Partage Twitter/X sans image | Ajouter `<meta name="twitter:card" content="summary_large_image" />` etc. |
| B9 | **Adresse visible (ligne 436) sans lien cliquable** | Expérience mobile dégradée (pas de tap-to-map) | Entourer l'adresse d'un lien `<a href="https://maps.google.com/?q=...">` |
| B10 | **Horaires incomplets** — lundi–vendredi uniquement, pas de mention du samedi | Les artisans travaillent souvent le samedi | Préciser l'état du samedi (fermé ou ouvert jusqu'à X) dans JSON-LD et HTML |
| B11 | **Section "Avis" sans lien vers la fiche Google** | Opportunité de conversion et de signal E-E-A-T manquée | Ajouter un lien "Voir tous nos avis sur Google → " pointant vers la fiche |
| B12 | **Image hero dupliquée** — `1000028623.jpg` chargée deux fois (hero + section avant/après) | Double requête HTTP | Compresser au maximum ; à terme utiliser la même source optimisée |

---

## 2. Analyse détaillée par section

### 2.1 SEO Technique

**Balise `<title>`**
- Valeur actuelle : `Harang Vincent — Peintre & Façadier | Charleville-Mézières, Ardennes` (69 car.)
- Problème : 9 caractères au-dessus du seuil de troncature Google (60 car.)
- Proposition : `Peintre Façadier Charleville-Mézières | Harang` (47 car.) — concis, mot-clé primaire en premier, marque à la fin

**Meta description**
- Valeur actuelle (185 car.) : *"Peintre en bâtiment et façadier dans les Ardennes. Ravalement de façade, peinture intérieure, marquage au sol. Devis gratuit. Zone : Charleville-Mézières, Villers-Semeuse et environs."*
- Proposition (~152 car.) : *"Ravalement de façade, peinture intérieure et marquage au sol à Charleville-Mézières et dans les Ardennes. Artisan 5/5 Google. Devis gratuit."*

**Hiérarchie des titres**
```
H1 : "Votre façade, impeccable."         ← pas de géo ❌
  H2 : "Ce que nous faisons"             ← générique ❌
    H3 : "Travaux extérieurs"
    H3 : "Travaux intérieurs"
  H2 : "La transformation parle d'elle-même"  ← générique ❌
  H2 : "Quelques chantiers récents"      ← générique ❌
  H2 : "Ce qu'ils en pensent"            ← générique ❌
  H2 : "Où nous intervenons"             ← ✅ (signal de zone)
  H2 : "Obtenez votre devis gratuit"     ← ✅ (CTA)
```
Problème principal : le H1 est purement stylistique/marketing ("Votre façade, impeccable.") — aucun signal géographique ni de métier. C'est le facteur on-page le plus pénalisant pour le SEO local.

**Structure URL**
Le site est mono-page (SPA à ancres). Les URLs `#prestations`, `#realisations`, etc. sont gérées par le navigateur et n'ont aucune valeur SEO distincte pour Google — c'est normal et sans impact négatif pour un site vitrine. Le seul point d'entrée SEO est `/`.

### 2.2 Données structurées (JSON-LD)

**Bilan :** syntaxe valide, champs essentiels présents. Enrichissements recommandés :

```json
{
  "@context": "https://schema.org",
  "@type": "HousePainter",
  "@id": "https://harang-facade.com/#business",
  "name": "Harang Vincent — Peintre & Façadier",
  "description": "Artisan peintre en bâtiment et façadier dans les Ardennes. Ravalement de façade, enduits, peinture intérieure, marquage au sol. Devis gratuit.",
  "image": "https://harang-facade.com/img/ravalement-facade-apres.webp",
  "url": "https://harang-facade.com",
  "telephone": "+33616863312",
  "priceRange": "€€",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "9 Rue Albert Poulain",
    "addressLocality": "Villers-Semeuse",
    "postalCode": "08000",
    "addressCountry": "FR"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": 49.747,
    "longitude": 4.723
  },
  "areaServed": [
    {"@type": "City", "name": "Charleville-Mézières"},
    {"@type": "City", "name": "Villers-Semeuse"},
    {"@type": "AdministrativeArea", "name": "Ardennes"}
  ],
  "openingHoursSpecification": [{
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": ["Monday","Tuesday","Wednesday","Thursday","Friday"],
    "opens": "08:00",
    "closes": "18:30"
  }],
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "5",
    "reviewCount": "6"
  },
  "sameAs": [
    "https://maps.google.com/?cid=VOTRE_CID_GOOGLE_BUSINESS"
  ]
}
```

**Note :** vérifier les coordonnées GPS exactes de l'adresse avant de les inclure. Le CID Google Business se trouve dans l'URL de la fiche Google Maps du client.

### 2.3 SEO On-page / Contenu

**Mots-clés locaux cibles (non ou sous-exploités) :**

| Mot-clé | Volume estimé | Présent dans H1 | Dans H2 | Dans corps |
|---------|---------------|-----------------|---------|------------|
| peintre Charleville-Mézières | ★★★ | ❌ | ❌ | ✅ (topbar/eyebrow) |
| ravalement façade Ardennes | ★★★ | ❌ | ❌ | ✅ (liste) |
| peintre en bâtiment Villers-Semeuse | ★★ | ❌ | ❌ | ❌ |
| façadier Charleville-Mézières | ★★ | ❌ | ❌ | ❌ |
| vitrerie Charleville-Mézières | ★ | ❌ | ❌ | ❌ |
| marquage au sol Ardennes | ★ | ❌ | ❌ | ✅ (liste) |

**Services absents du contenu :**
- **Vitrerie** — mentionnée dans le brief métier, absente du site
- **Ponçage parquet** / **vitrification parquet** — présents depuis la dernière session mais sans développement (juste deux `<li>`)

**Richesse sémantique :** Le contenu des deux cartes prestations est en listes à puces sans description développée. Pour le SEO, idéalement chaque prestation majeure mérite 2-3 phrases expliquant le processus, les matériaux, les cas d'usage locaux. Cela augmente la couverture sémantique et le E-E-A-T.

### 2.4 SEO Local

| Point | Statut |
|-------|--------|
| NAP (Nom/Adresse/Téléphone) cohérente dans JSON-LD | ✅ (après correction du n°9) |
| NAP dans le HTML visible | ⚠️ Rue sans numéro (ligne 436) |
| Téléphone cliquable (`tel:+33616863312`) | ✅ |
| Horaires visibles | ✅ (topbar + contact) |
| Google Maps embed | ❌ Absent |
| Fiche Google Business active | ✅ (indiqué par le client) |
| Lien vers la fiche depuis le site | ❌ Absent |
| Citations/annuaires externes | Non audité (périmètre hors code) |

**Annuaires locaux à viser :** Pages Jaunes, Kompass, Houzz (travaux), Habitissimo, Leroy Merlin pro, Chambre des Métiers Ardennes, Yelp France.

### 2.5 Tracking & Mesure

| Outil | Statut |
|-------|--------|
| Google Analytics 4 | ❌ Absent |
| Google Search Console | ❌ Non vérifié |
| Google Tag Manager | ❌ Absent |
| Meta Pixel | ❌ Absent (non prioritaire) |

**Action immédiate :**
1. Créer une propriété GA4 sur analytics.google.com
2. Ajouter le snippet `gtag.js` dans le `<head>` de `index.html`
3. Vérifier la propriété dans Search Console (méthode recommandée : balise meta ou via GA4)
4. Soumettre `https://harang-facade.com/sitemap.xml` dans Search Console

### 2.6 Performance & Mobile

**Images — bilan des tailles actuelles :**

| Fichier | Taille | Usage | Commentaire |
|---------|--------|-------|-------------|
| 1000028623.jpg | 305 KB | Hero + Avant/Après "Après" | Image LCP — priorité absolue |
| 1000028626.jpg | 341 KB | Avant/Après "Avant" | Au-dessus du fold mobile |
| 1000028625.jpg | 401 KB | Galerie parking PMR | La plus lourde |
| 1000028624.jpg | 182 KB | Galerie combles | Acceptable |
| 1000028627.jpg | 143 KB | Galerie cuisine | Acceptable |
| **Total** | **1 372 KB** | | ~1,34 MB de JPG |

**Objectifs après optimisation WebP :** < 80 KB pour le hero, < 150 KB par image de galerie.

**Points positifs :** JS minimal (vanilla, pas de framework), CSS custom sans librairie lourde, `loading="lazy"` sur toutes les images hors hero.

**Points à corriger :**
- Ajouter `width` et `height` sur chaque `<img>` pour éviter le CLS
- Ajouter `<link rel="preload" as="image">` pour l'image hero
- Envisager `font-display: swap` ou `optional` pour les Google Fonts (ou auto-hébergement)

### 2.7 Accessibilité impactant le SEO

| Point | Statut |
|-------|--------|
| Labels de formulaire associés aux champs (`for`/`id`) | ✅ Tous présents |
| `aria-label="Menu"` sur le bouton hamburger | ✅ |
| `aria-hidden="true"` sur le honeypot | ✅ |
| Alt text descriptifs sur les images | ✅ (mais génériques — voir §2.3) |
| HTML sémantique (header, nav, section, footer) | ✅ |
| Liens de navigation avec texte descriptif | ✅ |
| `lang="fr"` sur `<html>` | ✅ |

Accessibilité globalement correcte. Seule amélioration utile : les alt des images de galerie pourraient inclure le nom de la ville (*"Rénovation cuisine Charleville-Mézières"* plutôt que *"Rénovation cuisine — peinture intérieure"*) — double bénéfice SEO + accessibilité.

---

## 3. Plan d'action priorisé

### 🚀 Quick wins (< 2h, impact immédiat)

1. **Corriger le NAP HTML** (ligne 436) : ajouter "9" devant "Rue Albert Poulain"
2. **Ajouter la balise canonical** dans `<head>`
3. **Raccourcir le title** (47-55 caractères, mot-clé géo en premier)
4. **Raccourcir la meta description** (≤ 155 caractères)
5. **Ajouter Open Graph** (og:title, og:description, og:image, og:url)
6. **Ajouter width/height sur toutes les images** (valeurs réelles à mesurer)
7. **Ajouter `<link rel="preload">` pour le hero**

### 🔧 Chantiers de fond (priorité 1 — < 1 semaine)

8. **Installer GA4** + vérifier la Search Console + soumettre le sitemap
9. **Réécrire le H1** avec mot-clé géographique — ex. : `"Peintre & Façadier à Charleville-Mézières"` (l'accroche marketing peut descendre en sous-titre `<p>`)
10. **Optimiser et renommer les images** (WebP, < 80-150 KB, noms descriptifs)
11. **Enrichir le JSON-LD** : ajouter `geo`, `sameAs`, `description`, `areaServed` en objets typés
12. **Corriger H2** pour y intégrer les mots-clés locaux

### 📈 Chantiers de fond (priorité 2 — < 1 mois)

13. **Compléter la zone d'intervention** (15-20 communes Ardennes)
14. **Ajouter le service vitrerie** dans les prestations
15. **Enrichir le contenu** des prestations (2-3 phrases par service majeur)
16. **Ajouter un embed Google Maps** dans la section Contact ou Zone
17. **Ajouter un lien vers la fiche Google** dans la section Avis
18. **Créer des citations locales** sur Pages Jaunes, Houzz, Habitissimo
19. **Ajouter `<lastmod>` dans sitemap.xml**
20. **Passer les Google Fonts en auto-hébergement** (ou `font-display: optional`)

---

## 4. Tests en ligne à lancer

Ces tests nécessitent un accès en ligne et ne peuvent pas être exécutés depuis le code source. À lancer dans cet ordre :

| Outil | URL | Quoi vérifier |
|-------|-----|---------------|
| **PageSpeed Insights** | https://pagespeed.web.dev/report?url=https://harang-facade.com/ | Scores CWV (LCP, INP, CLS), opportunités d'optimisation images |
| **Google Rich Results Test** | https://search.google.com/test/rich-results?url=https://harang-facade.com/ | Validation JSON-LD HousePainter, AggregateRating |
| **Google Mobile-Friendly Test** | https://search.google.com/test/mobile-friendly?url=https://harang-facade.com/ | Rendu mobile, tap targets |
| **Schema Validator** | https://validator.schema.org/#url=https://harang-facade.com/ | Erreurs/warnings sur les données structurées |
| **WebPageTest** | https://www.webpagetest.org/ | TTFB, waterfall de chargement, LCP réel |
| **Google Search Console** | https://search.google.com/search-console | Indexation, positions, erreurs de crawl |
| **site:harang-facade.com** | Taper dans Google | Vérifier combien de pages sont indexées |

---

## 5. Note sur ce qui est déjà bien fait

- ✅ HTTPS actif (Vercel SSL)
- ✅ `lang="fr"` sur `<html>`
- ✅ Viewport configuré
- ✅ JSON-LD HousePainter avec AggregateRating
- ✅ robots.txt avec pointeur sitemap
- ✅ sitemap.xml valide (format correct)
- ✅ `loading="lazy"` sur toutes les images sous le fold
- ✅ `aria-label`, labels de formulaire, HTML sémantique
- ✅ JS minimal (vanilla), CSS propre sans librairie
- ✅ Formulaire Formspree avec honeypot anti-spam
- ✅ Téléphone cliquable (`tel:`)
- ✅ Adresse et horaires visibles dans le HTML
- ✅ 6 avis clients 5/5 affichés
