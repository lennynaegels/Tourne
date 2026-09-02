# Tournée — version 7

Carnet de bord pour livreurs indépendants. Suivi des courses au GPS, calcul du
net réel, échéances URSSAF, démarches administratives et objectif de chiffre
qui se recalcule tout seul.

Application web installable sur le téléphone. Aucun compte, aucun serveur :
tout reste sur l'appareil.

---

## Mise en ligne

Les 9 fichiers vont **à la racine du dépôt**. Il n'y a aucun dossier à créer.

1. Crée un dépôt **public** sur GitHub
2. *Add file → Upload files*, sélectionne les 9 fichiers, *Commit changes*
3. *Settings → Pages → Deploy from a branch → `main` → `/ (root)`*
4. Attends une à deux minutes, puis ouvre l'adresse sur ton téléphone
5. Menu ⋮ → *Ajouter à l'écran d'accueil*

À la racine tu dois voir exactement ceci, et rien d'autre :

```
index.html              l'application entière
test.html               page de diagnostic
sw.js                   fonctionnement hors ligne
manifest.webmanifest    installation sur l'écran d'accueil
reglementation.json     taux, seuils, échéances — le fichier à mettre à jour
icon-192.png
icon-512.png
icon-maskable-512.png
README.md
```

Si `index.html` se retrouve dans un sous-dossier, la page ne s'affichera pas.

### Vérifier que c'est bien la bonne version

Ouvre l'app → **Réglages** → tout en bas → bloc **Version**. Il doit indiquer
« Tournée version 7 ».

Si ce n'est pas le cas, appuie sur **Forcer la mise à jour** juste en dessous :
le cache hors ligne est effacé et la page rechargée. Tes courses et tes
réglages ne sont pas touchés.

### En cas de problème

Ouvre `ton-adresse/test.html` dans le navigateur. Cette page ne dépend de rien
d'extérieur : elle teste le stockage, le GPS, le réseau, le cache, et affiche
la version réellement présente sur le serveur.

---

## Les cinq écrans

### Course

Carte plein écran, chrono, et le minimum de gestes.

- **Démarrer le shift** lance le chrono. C'est lui qui sert de base au net
  horaire : le temps d'attente compte, c'est ce qui distingue un vrai
  net/heure d'une moyenne flatteuse.
- **Nouvelle course** — tu saisis le montant proposé et la distance annoncée,
  le verdict tombe immédiatement.
- **Course livrée** — le montant est pré-rempli, la distance mesurée au GPS.
- Sous les chiffres du jour, un bandeau rappelle l'objectif du jour.

*Démarrer sans évaluer* lance une course à vide si tu préfères ne rien saisir
en roulant ; tu compléteras le soir depuis l'historique.

### Statut

Tout l'administratif.

- **Parcours de création** — 7 démarches cochables, chacune dépliable :
  pourquoi, comment, pièces à préparer, coût, lien officiel. Celles à délai
  contraint affichent les jours restants. Chaque étape peut être écartée d'un
  « cette étape ne me concerne pas » et sort alors du décompte.
- **Déclaration assistée** — l'app additionne tes encaissements de la période
  et affiche le montant exact à recopier, arrondi à l'euro. Un bouton le copie,
  un autre ouvre l'espace URSSAF.
- **Jauges de plafonds** — régime micro (proratisé l'année de création) et
  franchise de TVA, séparées car ce sont deux seuils distincts.
- **Registre des recettes** — obligation comptable, export CSV.
- **Glossaire** — abattement, franchise, versement libératoire, en clair.

### Courses

Historique par shift, avec le détail de chaque course. Tout est modifiable
après coup : montant, pourboire, distance.

### Stats

- **Grand objectif** en tête (voir plus bas)
- Net encaissé, net par heure, nombre de courses, distance
- Le rapport de chaque heure de la journée et de chaque jour de la semaine
- Où part le brut : cotisations, coût véhicule, net réel
- Provision à mettre de côté

### Réglages

Véhicule, seuils d'acceptation, objectifs, provisions, plateformes,
réglementation, sauvegarde, diagnostic, version.

---

## Le grand objectif

Tu fixes un montant et une échéance, l'app calcule ce qu'il faut faire par jour.

**Rien n'est jamais figé.** À chaque consultation elle repart de ce qui reste
à faire, divisé par le nombre de jours travaillables restants. Une journée sans
course relève automatiquement les suivantes ; une grosse journée les allège.

Réglages disponibles :

| Réglage | Effet |
|---|---|
| Montant visé | Le chiffre à atteindre |
| Compté en | Brut encaissé, ou net après charges |
| Du / Au | La période |
| Jours où tu peux livrer | Sept touches L M M J V S D |

Le choix des jours change tout : 35 000 € sur un an du lundi au vendredi
donnent 133,81 € par jour ; en ajoutant le samedi, 111,58 €.

La carte des Stats montre aussi l'avance ou le retard en euros, et la
projection au rythme réellement tenu. Si l'objectif devient hors d'atteinte,
elle le dit et propose les trois leviers : monter le montant journalier,
ajouter des jours, ou revoir la cible.

---

## Mettre à jour la loi sans toucher au code

Aucun taux, aucun seuil, aucune échéance, aucune démarche n'est écrit dans le
programme. Tout vit dans `reglementation.json`.

Trois niveaux, du plus prioritaire au moins prioritaire :

1. **Un fichier importé** depuis Réglages → Réglementation
2. **`reglementation.json`** à la racine du dépôt
3. **Le barème embarqué** dans `index.html`

Quand un taux change : tu modifies `reglementation.json` sur GitHub, ou tu
télécharges le barème actuel depuis l'app, tu l'édites et tu le réimportes.
Aucune ligne de code à toucher.

Un fichier importé est validé avant d'être accepté : schéma reconnu, au moins
un régime, champs numériques cohérents. Un taux au-dessus de 100 % est refusé
avec le motif précis. Passé un an sans vérification, l'app affiche un rappel.

### Contenu du fichier

| Bloc | Contenu |
|---|---|
| `regimes` | Cotisations, CFP, abattement, versement libératoire, plafond, seuils de TVA |
| `acre` | Exonération, durée, délai de demande, conditions |
| `declaration` | Délai de première déclaration, pénalité, échéances trimestrielles |
| `plafonds` | Règle de prorata, années de dépassement tolérées |
| `parcours` | Les démarches : titre, échéance, explication, marche à suivre, pièces, lien |
| `obligations` | Ce qui revient chaque année |
| `fiches` | Le glossaire |

---

## Valeurs livrées, vérifiées le 28 août 2026

| Paramètre | Valeur |
|---|---|
| Cotisations, prestations de services BIC | 21,2 % |
| Cotisations, vente de marchandises | 12,3 % |
| Cotisations, libéral BNC | 25,6 % |
| ACRE, créations depuis le 1er juillet 2026 | 25 % pendant 12 mois |
| Délai de demande d'ACRE | 60 jours |
| Plafond micro, services | 83 600 € |
| Plafond micro, vente | 203 100 € |
| Franchise de TVA, services | 37 500 € — majoré 41 250 € |
| Abattement fiscal, services BIC | 50 % |
| Délai de première déclaration | 90 jours |
| Pénalité de déclaration manquante | 60,10 € |

La livraison de repas relève des prestations de services BIC, code APE 5320Z.

Ces valeurs sont révisées chaque janvier. Vérifie-les sur
[autoentrepreneur.urssaf.fr](https://www.autoentrepreneur.urssaf.fr).

---

## Sauvegarde

Les données vivent dans le stockage local du navigateur. Vider les données de
navigation les efface. Exporte régulièrement depuis **Réglages → Tes données →
Exporter une sauvegarde** : tu obtiens un `.json` que *Restaurer une
sauvegarde* relit tel quel.

---

## Précision du GPS

Les distances s'accumulent par segments entre deux points successifs. Trois
filtres écartent la dérive : précision annoncée au-delà de 35 m, déplacements
de moins de 6 m, sauts dépassant 35 m/s. En ville dense, compte 2 à 5 % d'écart
avec un compteur de vélo. La distance reste modifiable à la main.

Le GPS exige une connexion `https`. GitHub Pages la fournit ; un fichier ouvert
directement depuis le disque ne fonctionnera pas.

---

## Ce que l'application ne fait pas

**Elle ne reçoit pas les propositions de course.** Aucune plateforme n'expose
d'interface publique côté livreur : les API d'Uber Eats s'adressent aux
restaurants et aux partenaires logistiques. Les contournements qui lisent
l'écran ou les notifications de l'application officielle violent les conditions
d'utilisation et exposent à une désactivation de compte. Les offres continuent
donc d'arriver dans l'application de la plateforme ; tout le reste se passe ici.

L'écran Statut est un aide-mémoire, pas un conseil juridique ou comptable. Pour
une situation particulière, l'URSSAF répond au **3698**.

---

## Sous le capot

Pas de framework, pas de compilation, pas de dépendance à installer. Deux
ressources externes chargées depuis un CDN : Leaflet pour la carte et deux
polices Google. La carte n'est pas une dépendance : si elle ne charge pas,
l'application continue de fonctionner et le GPS mesure les kilomètres
normalement.

## Licence

MIT
