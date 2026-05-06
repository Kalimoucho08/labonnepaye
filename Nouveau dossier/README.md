# 🎲 La Bonne Paye - Assistant Banquier

**Webapp monofichier** pour aider le banquier dans le jeu *La Bonne Paye* à gérer les prêts, l'épargne et le Jour de Paye.

## 🚀 Fonctionnalités

### ✅ **MVP implémenté**
- **Paramétrage complet** avec presets Francs/Euros/Personnalisé
- **Gestion des joueurs** (création, modification, suppression)
- **Tableau de bord global** avec statistiques
- **Fiche joueur détaillée** avec actions rapides
- **Assistant Jour de Paye** (wizard étape par étape)
- **Historique des opérations** avec filtres
- **Export/Import** JSON et CSV
- **Sauvegarde automatique** dans le navigateur
- **Thème clair/sombre**
- **Interface responsive** (tablette, desktop)

### 📊 **Calculs métier**
- Gestion des prêts (capital et intérêts)
- Gestion de l'épargne (dépôts, retraits, intérêts)
- Calcul automatique des intérêts (épargne et prêt)
- Validation des règles métier
- Séquence Jour de Paye conforme aux règles

## 🎯 **Objectif**

Soulager le joueur qui tient la banque en réduisant :
- ❌ Les erreurs de calcul
- ❌ Les oublis de séquence au Jour de Paye
- ❌ Le suivi manuel des prêts et livrets

## 📁 **Structure**

Fichier unique : `labonnepaye.html`
- HTML + CSS + JavaScript intégrés
- Aucune dépendance externe
- Utilisable hors ligne
- Persistance via localStorage

## 🖥️ **Utilisation**

### 1. **Premier lancement**
1. Ouvrez `labonnepaye.html` dans un navigateur moderne
2. Configurez les règles de la partie (onglet **Paramétrage**)
   - Choisissez un preset : **Francs** (ancienne version) ou **Euros**
   - Ou personnalisez les montants et taux
3. Cliquez sur *"Enregistrer les paramètres et commencer"*

### 2. **Ajouter des joueurs**
1. Allez dans l'onglet **Joueurs**
2. Cliquez sur *"Ajouter un joueur"*
3. Renseignez nom, couleur, liquidité et épargne initiale
4. Répétez pour tous les joueurs

### 3. **Gérer la partie**
- **Tableau de bord** : vue d'ensemble de tous les joueurs
- **Actions rapides** : salaire, prêt, dépôt, retrait, etc.
- **Fiche joueur** : actions détaillées pour un joueur spécifique

### 4. **Jour de Paye**
1. Allez dans l'onglet **Jour de Paye**
2. Cliquez sur *"Commencer l'assistant"*
3. Suivez les 7 étapes pour chaque joueur :
   - Résumé avant paie
   - Paiement du salaire
   - Intérêts d'épargne
   - Intérêts de prêt
   - Remboursement du prêt
   - Nouvel état financier
   - Clôture du mois

### 5. **Historique et export**
- **Historique** : toutes les opérations avec filtres
- **Export JSON** : sauvegarde complète de la partie
- **Export CSV** : opérations et joueurs pour Excel
- **Import JSON** : restaurer une sauvegarde

## ⚙️ **Règles prises en charge**

### **Presets inclus**
| Paramètre | Version Francs | Version Euros |
|-----------|----------------|---------------|
| Salaire | 6 500 ₣ | 650 € |
| Tranche prêt | 10 000 ₣ | 1 000 € |
| Taux épargne | 10% | 10% |
| Taux prêt | 10% | 10% |
| Frais retrait | 1 000 ₣ | 100 € |
| Dépôt avant case | 23 | 23 |

### **Variantes personnalisables**
- Montants du salaire et des prêts
- Taux d'intérêt (épargne et prêt)
- Frais de retrait du livret
- Date limite pour les dépôts
- Remboursement uniquement au Jour de Paye
- Dépôt autorisé après date limite (sans intérêts)

## 🧪 **Tests métier validés**

### **Scénarios testés**
- Prêt simple avec intérêts
- Dépôt et retrait d'épargne avec frais
- Remboursement partiel et total
- Calcul correct des intérêts (épargne et prêt)
- Séquence complète du Jour de Paye
- Persistance des données (rechargement page)

### **Validation**
- Empêche les retraits supérieurs à l'épargne
- Empêche les remboursements supérieurs à la dette
- Vérifie les fonds suffisants pour les paiements
- Signale les incohérences métier

## 📱 **Compatibilité**

- **Navigateurs** : Chrome, Firefox, Safari, Edge (versions récentes)
- **Devices** : Desktop, tablette, mobile (interface responsive)
- **Systèmes** : Windows, macOS, Linux, iOS, Android
- **Hors ligne** : utilisation sans connexion internet

## 🔧 **Pour les développeurs**

### **Structure du code**
```javascript
// Modules internes
Config    // Paramètres et presets
State     // État global et gestion des données
Store     // Persistance localStorage
Calc      // Calculs métier
Actions   // Opérations métier
UI        // Interface utilisateur
```

### **Modèle de données**
```javascript
{
  meta: { title, createdAt, version },
  rules: { currency, paydaySalary, loanUnit, ... },
  players: [{ id, name, color, cash, savings, debt, ... }],
  operations: [{ id, timestamp, playerId, category, amount, ... }],
  bank: { pot },
  ui: { currentMonth, selectedPlayerId, ... }
}
```

### **Extension possible**
- Ajout de nouvelles variantes de règles
- Intégration avec API externe
- Mode multi-joueur réseau
- Statistiques avancées
- Automatisation des cartes Courrier

## 📄 **Licence**

Projet open-source à usage éducatif et personnel.

## 🙏 **Remerciements**

Basé sur les règles officielles du jeu *La Bonne Paye* (Milton Bradley/Hasbro).

---

**💡 Astuce** : Pour une sauvegarde permanente, utilisez l'export JSON et stockez le fichier sur votre ordinateur.