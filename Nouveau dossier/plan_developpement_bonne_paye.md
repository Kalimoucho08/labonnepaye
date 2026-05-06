# Plan de développement — Webapp mono-fichier pour le banquier de *La Bonne Paye*

Cette webapp doit être pensée comme un compagnon de partie local, rapide à ouvrir et configurable selon plusieurs éditions ou variantes maison. Les règles consultées montrent des écarts réels entre versions en francs et en euros, notamment sur le salaire de Jour de Paye, le montant des prêts, les frais de retrait d’épargne, et certaines contraintes de dépôt ou de remboursement. [cite:12][cite:4][cite:17][cite:6]

## 1. Objectif produit

L’objectif n’est pas de recréer tout le jeu, mais de soulager le joueur qui tient la banque en réduisant les erreurs de calcul, les oublis de séquence au Jour de Paye et le suivi manuel des prêts et des livrets. Les sources consultées confirment que le banquier gère la paie, les prêts, les intérêts, les retraits d’épargne et différents flux d’argent sur toute la partie. [cite:4][cite:27][cite:35]

### Résultat attendu

Une application HTML/CSS/JavaScript en un seul fichier, utilisable hors ligne dans un navigateur, sans backend, avec état de partie manipulable localement. Cette forme est adaptée à un usage sur ordinateur de table, vidéoprojecteur ou tablette pendant une partie.

## 2. Périmètre fonctionnel

### MVP — version 1

Le premier périmètre doit couvrir uniquement le cœur du rôle du banquier :

- création d’une partie ;
- paramétrage des règles ;
- création des joueurs ;
- suivi de la trésorerie de chaque joueur ;
- gestion des prêts ;
- gestion du livret d’épargne ;
- calcul des intérêts ;
- assistant “Jour de Paye” ;
- historique des opérations ;
- impression ou export léger.

Ce périmètre est directement justifié par les règles, qui imposent au banquier la gestion du salaire, des prêts, des intérêts de prêt, des intérêts d’épargne et des retraits d’argent. [cite:4][cite:27][cite:35][cite:6]

### Hors périmètre initial

Ces fonctions peuvent être reportées après la v1 :

- simulation complète du plateau ;
- automatisation détaillée de toutes les cartes Courrier/Transactions ;
- mode multijoueur réseau ;
- synchronisation cloud ;
- comptes utilisateurs.

## 3. Besoins métier détaillés

### 3.1 Paramétrage de partie

Le paramétrage doit être la première étape obligatoire, car les sources montrent que plusieurs éditions utilisent des montants différents, par exemple 650 euros ou 6500 francs pour la paie, et 1500 euros ou 10 000 francs pour certains prêts selon l’édition. [cite:12][cite:4][cite:17]

#### Paramètres obligatoires

- nom de la partie ;
- devise affichée (francs, euros, texte libre) ;
- montant du salaire de Jour de Paye ;
- montant d’une tranche de prêt ;
- taux d’intérêt du livret ;
- taux d’intérêt du prêt ;
- frais de retrait du livret ;
- dépôt autorisé jusqu’à quelle case/jour ;
- nombre de mois de la partie ;
- nombre de joueurs.

#### Paramètres avancés

- autoriser ou non le dépôt après une certaine date ;
- autoriser ou non le remboursement de prêt hors Jour de Paye ;
- durée sans remboursement obligatoire après contraction d’un prêt ;
- arrondi des intérêts ;
- montants spéciaux récurrents, par exemple pot, caisse, primes, pénalités.

#### Presets

Prévoir au moins trois profils :

- ancienne version francs ;
- version euros ;
- personnalisé.

Les différences entre éditions rendent les presets particulièrement utiles pour accélérer la mise en route d’une partie. [cite:12][cite:17]

### 3.2 Gestion des joueurs

Pour chaque joueur, la webapp doit conserver :

- nom ;
- couleur ou identifiant visuel ;
- argent liquide actuel ;
- solde du livret ;
- dette totale ;
- nombre de cartes prêt ;
- intérêts dus au prochain Jour de Paye ;
- opérations du mois ;
- statut du mois en cours.

Les règles consultées montrent que le joueur peut avoir de l’épargne, une dette, des intérêts à payer et des événements à régler au Jour de Paye. [cite:4][cite:35]

### 3.3 Journal d’opérations

Le journal d’opérations est le cœur comptable de l’application. Toutes les données calculées doivent en dériver.

#### Champs recommandés

- identifiant ;
- horodatage local ;
- mois / tour / jour ;
- joueur concerné ;
- catégorie d’opération ;
- sous-type ;
- montant ;
- sens du flux (banque vers joueur, joueur vers banque, joueur vers pot) ;
- indicateur “compte pour intérêts d’épargne du mois” ;
- commentaire ;
- annulation éventuelle.

#### Types d’opérations minimaux

- salaire ;
- dépôt livret ;
- retrait livret ;
- intérêt livret ;
- prêt contracté ;
- intérêt prêt ;
- remboursement prêt ;
- paiement facture ;
- gain ;
- contribution au pot ;
- gain du pot ;
- correction manuelle.

Cette granularité est utile car les règles distinguent bien le capital emprunté, les intérêts, l’épargne et les autres mouvements du mois. [cite:4][cite:27][cite:35]

### 3.4 Assistant Jour de Paye

Les règles décrivent une séquence précise au Jour de Paye : encaisser le salaire, toucher les intérêts d’épargne, payer les intérêts des prêts, puis rembourser éventuellement une partie ou la totalité des prêts. [cite:4][cite:27][cite:35]

L’application doit donc proposer un assistant guidé par joueur avec étapes validables une par une.

#### Étapes du wizard

1. Afficher le résumé du joueur avant paie.
2. Ajouter le salaire.
3. Calculer et proposer les intérêts d’épargne.
4. Calculer et exiger les intérêts de prêt.
5. Proposer un remboursement partiel ou total.
6. Afficher le nouvel état financier.
7. Clôturer le mois du joueur.

### 3.5 Règles de validation

Le moteur de validation doit empêcher certaines incohérences ou au moins les signaler :

- emprunter alors que le joueur dispose d’une épargne suffisante, si la variante l’interdit ; [cite:4]
- déposer sur le livret après la date limite si la règle active l’interdit ; [cite:4]
- rembourser plus que la dette restante ;
- rendre le solde du livret négatif ;
- supprimer une ligne déjà prise en compte sans recalcul global ;
- mélanger plusieurs variantes sans confirmation.

## 4. Architecture technique

### 4.1 Contrainte générale

L’application doit tenir dans un seul fichier `.html`, avec CSS et JavaScript embarqués. Cette contrainte facilite la diffusion, la sauvegarde et l’ouverture locale, notamment dans un contexte familial, scolaire ou associatif.

### 4.2 Architecture logique

Le code peut être structuré en modules internes simples :

- `config` : règles de la partie ;
- `state` : état courant ;
- `store` : chargement / sauvegarde ;
- `calc` : calculs métier ;
- `ui` : rendu ;
- `actions` : mutations ;
- `validators` : contrôles ;
- `exporters` : impression, JSON, CSV.

### 4.3 Modèle de données

```js
const game = {
  meta: { title: '', createdAt: '', version: 1 },
  rules: {
    currency: 'francs',
    paydaySalary: 650,
    loanUnit: 1000,
    savingsInterestRate: 0.10,
    loanInterestRate: 0.10,
    savingsWithdrawalFee: 100,
    savingsDepositDeadlineDay: 23,
    monthsTotal: 12,
    loanRepaymentOnlyOnPayday: true
  },
  players: [],
  operations: [],
  bank: { pot: 0 },
  ui: { currentMonth: 1, selectedPlayerId: null }
}
```

Ce modèle couvre les principales variations de règles trouvées dans les sources et permet d’ajouter des variantes sans casser l’existant. [cite:12][cite:4][cite:17]

## 5. Calculs métier à implémenter

### 5.1 Solde d’épargne

Le solde d’épargne d’un joueur correspond à la somme des dépôts et intérêts, moins les retraits et frais associés. Certaines règles imposent aussi une limite de date pour que le dépôt compte dans le calcul des intérêts du mois. [cite:4]

### 5.2 Dette totale

La dette totale correspond au cumul des prêts moins les remboursements de capital. Les intérêts ne doivent pas être fusionnés avec le capital restant dû. [cite:35][cite:5]

### 5.3 Intérêt de prêt

Le moteur calcule l’intérêt comme un pourcentage de la dette encore due au Jour de Paye. Plusieurs sources consultées mentionnent 10 % dans les variantes françaises récentes et anciennes recensées. [cite:35][cite:5][cite:4]

### 5.4 Intérêt de livret

Le moteur calcule l’intérêt comme un pourcentage de l’épargne éligible au Jour de Paye. La règle ancienne en francs précise qu’un dépôt après la case 23 ne donne pas droit à l’intérêt de fin de mois. [cite:4]

## 6. UX et écrans

### 6.1 Écrans à produire

- écran d’accueil ;
- écran de paramétrage ;
- écran de création des joueurs ;
- tableau de bord global ;
- fiche joueur ;
- écran de saisie rapide ;
- assistant Jour de Paye ;
- historique / audit ;
- écran export / impression.

### 6.2 UX prioritaire

Le produit doit être optimisé pour un usage “banquier en partie”, donc avec :

- gros boutons d’action ;
- raccourcis d’opération fréquente ;
- lecture claire des montants ;
- faible profondeur de navigation ;
- possibilité de corriger vite une erreur.

## 7. Stratégie de réalisation

### Phase 1 — Spécification

- figer le périmètre v1 ;
- lister les variantes de règles à prendre en charge ;
- rédiger les objets de données ;
- écrire les cas de calcul ;
- définir les écrans.

### Phase 2 — Prototype logique

- créer le fichier HTML minimal ;
- implémenter l’état en mémoire ;
- coder les opérations de base ;
- vérifier les calculs sur 2 à 3 scénarios de partie.

### Phase 3 — Interface utilisateur

- construire le tableau de bord ;
- ajouter la fiche joueur ;
- intégrer la saisie rapide ;
- intégrer l’assistant Jour de Paye ;
- améliorer l’impression.

### Phase 4 — Robustesse

- validation métier ;
- annulation / suppression ;
- import/export JSON ;
- export CSV ;
- tests de non-régression sur variantes francs/euros.

### Phase 5 — Finition

- responsive tablette ;
- thème clair/sombre ;
- textes d’aide ;
- presets prêts à l’emploi ;
- optimisation ergonomique.

## 8. Backlog priorisé

### Priorité P1

- paramétrage complet ;
- joueurs ;
- journal d’opérations ;
- calcul dette / épargne ;
- assistant Jour de Paye.

### Priorité P2

- presets de variantes ;
- export JSON / CSV ;
- impression ;
- filtres historiques.

### Priorité P3

- gestion avancée du pot ;
- automatisation des cartes ;
- statistiques de fin de partie ;
- mode “animation en classe”.

## 9. Plan de tests

### Tests métier

- prêt simple ;
- plusieurs prêts ;
- remboursement partiel ;
- remboursement total ;
- dépôt avant date limite ;
- dépôt après date limite ;
- retrait avec frais ;
- intérêt d’épargne correct ;
- intérêt de prêt correct ;
- combinaison prêt + épargne sur le même joueur.

### Tests de variantes

- preset ancienne version francs ;
- preset version euros ;
- preset personnalisé 650 / 1000 / 10 %.

### Tests UX

- saisie complète en moins de 10 secondes pour une opération courante ;
- visibilité en plein écran ;
- impression lisible ;
- usage tablette en mode portrait.

## 10. Définition du succès

La v1 est réussie si un banquier peut gérer une partie entière sans papier pour les prêts, l’épargne et le Jour de Paye, avec moins d’erreurs de calcul et sans ambiguïté sur la variante utilisée. Cet objectif est cohérent avec la charge réellement décrite dans les règles consultées. [cite:4][cite:27][cite:35]
