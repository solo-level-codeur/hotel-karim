🛠 Rapport d'erreurs – Dump SQL reservation_hotel

Lors de l’exécution du script, j’ai constaté la présence de nombreuses erreurs affectant son bon fonctionnement. En voici un rapport.



##  Erreurs et incohérences 

### 1. 🔑 Clé unique incorrecte (`chambres`)
```sql
UNIQUE KEY `id_hotel` (`id_hotel`,`numChambre`)
```
- **Erreur** : `numChambre` n'existe pas. Le champ s'appelle `numero`.
- ✅ Corriger en : 
```sql
UNIQUE KEY `id_hotel` (`id_hotel`, `numero`)
```

---

### 2. 🔑 Doublon de clé unique (`couchages`)
```sql
UNIQUE KEY `nom` (`nom`),
UNIQUE KEY `nom_2` (`nom`)
```
- **Erreur** : Redondance. Deux clés uniques sur le même champ `nom`.
- ✅ Supprimer `UNIQUE KEY nom_2`.

---

### 3. ⚠️ Mauvais mot-clé dans `ALTER TABLE` (`chambres`, `chambre_type_couchage`, `tarifs`)
```sql
ADD CONSTRAINT `nom` KEY (`colonne`) REFERENCES ...
```
- **Erreur** : Le mot-clé `KEY` n’a rien à faire ici. On doit utiliser `FOREIGN KEY`.
- ✅ Exemple corrigé :
```sql
ADD CONSTRAINT `chambres_ibfk_1` FOREIGN KEY (`id_type`) REFERENCES `chambre_types` (`id`)
```

---

### 4. ⚠️ Manque d'`AUTO_INCREMENT` (`hotels`, `chambre_types`, `tarifs`)
- `hotels.id`, `chambre_types.id`, et `tarifs.id` sont des identifiants primaires mais ne sont **pas auto-incrémentés**.
- ✅ Ajouter `AUTO_INCREMENT` si les valeurs sont générées automatiquement :
```sql
`id` int(11) UNSIGNED NOT NULL AUTO_INCREMENT
```

---

### 5. ⚠️ Cohérence du moteur de stockage
- Toutes les tables utilisent **MyISAM**, ce qui **ne supporte pas les contraintes de clés étrangères**.
- ⚠️ Tu définis des `FOREIGN KEY`, mais **elles ne seront pas prises en compte avec MyISAM**.
- ✅ Solution :
  - Utiliser `InnoDB` pour les tables qui nécessitent des contraintes d'intégrité référentielle.


## ✅ Résumé des corrections à faire

| Type            | Détail                                          | Action                        |
|-----------------|--------------------------------------------------|-------------------------------|
| Clé UNIQUE      | `numChambre` n'existe                           | Changer en `numero`           |
| Doublon         | Deux `UNIQUE KEY` sur `couchages.nom`           | Supprimer une des deux        |
| Syntaxe SQL     | Mauvais mot-clé `KEY` dans `ALTER TABLE`        | Utiliser `FOREIGN KEY`        |
| AUTO_INCREMENT  | Manque sur certaines tables avec PK             | Ajouter si nécessaire         |
| Moteur stockage | `MyISAM` empêche les contraintes FOREIGN KEY    | Remplacer par `InnoDB`        |

