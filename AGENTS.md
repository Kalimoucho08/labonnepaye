# 🏗️ Architecture - La Bonne Paye

## 📋 Vue d'ensemble

Application monofichier HTML/CSS/JS pour assister le banquier dans le jeu *La Bonne Paye*.

**Contraintes** : Fichier unique, hors ligne, persistance localStorage, responsive.

## 🎯 Objectifs métier

1. **Gérer les prêts** : capital, intérêts, remboursements
2. **Gérer l'épargne** : dépôts, retraits, intérêts
3. **Automatiser le Jour de Paye** : séquence salaire → intérêts → remboursement
4. **Suivre l'historique** : audit complet des opérations
5. **Supporter les variantes** : Francs, Euros, personnalisé

## 🏛️ Architecture technique

### Structure du fichier `labonnepaye.html`

```
<!DOCTYPE html>
<html>
  <head>
    <style>/* CSS complet */</style>
  </head>
  <body>
    <!-- Écrans (sections avec display:none/show) -->
    - screen-setup      : Paramétrage initial
    - screen-players    : Gestion joueurs
    - screen-dashboard  : Tableau de bord
    - screen-payday     : Assistant Jour de Paye
    - screen-history    : Historique opérations
    - screen-export     : Export/Import
    
    <script>
      // Modules JavaScript
      Config, State, Store, Calc, Actions, UI
    </script>
  </body>
</html>
```

### Modules JavaScript

#### **Config** - Paramètres et presets
```javascript
{
  presets: { francs, euros, custom },
  getDefaultRules(),
  applyPreset(presetName)
}
```

#### **State** - État global et gestion des données
```javascript
{
  game: { meta, rules, players, operations, bank, ui },
  getGame(), setGame(),
  addPlayer(), updatePlayer(), deletePlayer(),
  addOperation(), getPlayerOperations(),
  calculatePlayerDebt(), calculatePlayerSavings(),
  calculatePendingInterest()
}
```

#### **Store** - Persistance localStorage
```javascript
{
  STORAGE_KEY: 'labonnepaye_game_data',
  save(game), load(), clear(),
  exportToJSON(), importFromJSON(jsonString)
}
```

#### **Calc** - Calculs métier
```javascript
{
  formatCurrency(amount, currency),
  calculateLoanInterest(debt, interestRate),
  calculateSavingsInterest(savings, interestRate),
  applyWithdrawalFee(amount, fee),
  validateOperation(operation, gameState)
}
```

#### **Actions** - Opérations métier
```javascript
{
  paySalary(playerId, amount),
  grantLoan(playerId, amount),
  repayLoan(playerId, amount),
  depositSavings(playerId, amount),
  withdrawSavings(playerId, amount),
  payBill(playerId, amount, description),
  addGain(playerId, amount, description)
}
```

#### **UI** - Interface utilisateur
```javascript
{
  init(), bindEvents(),
  showScreen(screenName), renderCurrentScreen(),
  // Méthodes spécifiques par écran
  renderSetupScreen(), renderPlayersScreen(),
  renderDashboardScreen(), renderPaydayScreen(),
  renderHistoryScreen(), renderExportScreen(),
  // Méthodes d'actions
  saveSettings(), applyPreset(),
  showPlayerForm(), savePlayer(), editPlayer(),
  viewPlayerDetails(), promptPlayerAction(),
  executeQuickAction(),
  // Assistant Jour de Paye
  startPaydayWizard(), showPaydayStep(),
  nextPaydayStep(), prevPaydayStep(),
  completePaydayForPlayer(), cancelPaydayWizard(),
  // Modal et messages
  showModal(), hideModal(), showMessage()
}
```

## 📊 Flux de données

```
Utilisateur → UI → Actions → State → Store → UI (re-render)
                  ↓
               Validators
                  ↓
                Calc (calculs)
```

### Règles de validation
1. Empêche retrait > épargne
2. Empêche remboursement > dette
3. Vérifie fonds suffisants
4. Respecte séquence Jour de Paye
5. Applique frais de retrait

## 🎨 Interface utilisateur

### Design System
- **CSS custom** (pas de framework)
- **Thème clair/sombre** (toggle)
- **Responsive** (grid/flexbox)
- **Gros boutons** pour usage tactile
- **Cartes** pour regroupement visuel
- **Badges** pour états
- **Alertes** pour feedback

### Écrans principaux

#### 1. Paramétrage
- Presets francs/euros/personnalisé
- Configuration complète des règles
- Validation des valeurs

#### 2. Joueurs
- Création/modification/suppression
- Couleur personnalisée
- Soldes initiaux

#### 3. Tableau de bord
- Vue globale de tous les joueurs
- Statistiques agrégées
- Actions rapides
- Navigation vers fiche joueur

#### 4. Assistant Jour de Paye (case 31)
- **Wizard 7 étapes** déclenché individuellement par joueur
- **Séquence conforme** : salaire → intérêts épargne → intérêts prêt → remboursement
- **Pas de notion de mois** - chaque joueur avance à son rythme sur le plateau circulaire
- **Compteur de passages** à la case 31 par joueur (`paydayCount`)
- Calculs automatiques avec ajustements optionnels
- Prévisualisation avant validation

#### 5. Historique
- Tableau filtrable
- Annulation d'opérations
- Export CSV

#### 6. Export/Import
- JSON complet (sauvegarde)
- CSV des opérations/joueurs
- Import avec validation

## 🧪 Tests métier

### Scénarios validés
1. **Prêt simple** : accord → intérêts → remboursement
2. **Épargne** : dépôt → intérêts → retrait (frais)
3. **Jour de Paye complet** : salaire + intérêts ± remboursement
4. **Variantes** : francs vs euros
5. **Persistance** : rechargement page
6. **Validation** : erreurs bloquantes

### Calculs vérifiés
- Intérêts prêt = dette × taux
- Intérêts épargne = épargne × taux
- Frais retrait = montant - frais fixes
- Séquence Jour de Paye conforme règles

## 🔧 Points d'extension

### 1. Nouvelles fonctionnalités
- **Cartes Courrier** : automatisation
- **Pot commun** : gestion avancée
- **Statistiques** : graphiques fin de partie
- **Multi-langues** : traduction
- **Mode classe** : affichage grand écran

### 2. Améliorations techniques
- **Service Worker** : installation PWA
- **IndexedDB** : stockage plus robuste
- **WebSocket** : synchronisation multi-appareils
- **Tests unitaires** : couverture complète

### 3. Intégrations
- **API cloud** : sauvegarde en ligne
- **QR Code** : partage de partie
- **Impression** : fiche joueur format papier

## 🚀 Déploiement

### Simple
1. Copier `labonnepaye.html` sur un serveur web
2. Ouvrir dans un navigateur
3. Utiliser immédiatement

### Avancé
1. Minifier HTML/CSS/JS
2. Ajouter manifest PWA
3. Configurer service worker
4. Déployer sur CDN

## 📚 Documentation utilisateur

Voir `README.md` pour :
- Guide d'utilisation détaillé
- Présentation des fonctionnalités
- Exemples de scénarios
- FAQ et dépannage

## 🔍 Débogage

### Outils développeur
```javascript
// Accès aux modules depuis la console
window.LaBonnePaye.State.getGame()
window.LaBonnePaye.Store.exportToJSON()
```

### Common issues
1. **Données perdues** : vérifier localStorage quota
2. **Calculs incorrects** : vérifier règles actives
3. **UI non responsive** : vérifier console erreurs

## 📄 Licence et contributions

Projet éducatif open-source. Contributions bienvenues via issues/pull requests.

---

**Mainteneurs** : Suivre le plan de développement dans `plan_developpement_bonne_paye.md`