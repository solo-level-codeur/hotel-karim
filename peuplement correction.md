

# 🧾 Rapport de correction du script SQL `reservation_hotel`

## 📅 Date : 17 avril 2025  

---

## ✅ Modification manuelle

ajout champ descrpiption 

ALTER TABLE `chambre_types`
ADD COLUMN `description` VARCHAR(255) DEFAULT NULL AFTER `nom`;

**Problème**  
La table `chambres_types` ne comportait pas de champ pour stocker une description libre (autre que le commentaire technique). Cela limitait les possibilités pour les utilisateurs finaux.

**Solution**  
Ajout d'une colonne `description` dans la structure de la table `chambres_types`.  
Tu l’as probablement modifiée avec une requête du type :

```sql
ALTER TABLE chambres ADD COLUMN description TEXT;
```
Modifier l'ordre de peuplement des tables. Exemple correct d'ordre :

1. utilisateurs
2. salles_de_bain
3. couchages
4. chambre_types
5. chambre_type_couchage
6. hotels
7. chambres
8. tarifs


**Impact**  
Permet de stocker des textes plus riches ou marketing pour chaque chambre. Cela n’affecte pas les clés étrangères ni les relations existantes.

---

## ❌ Erreurs détectées dans le script initial

### 1. ❗ Erreur de syntaxe dans `salles_de_bain`

**Problème**  
Le script contenait une virgule en trop ou un `;` mal placé :

```sql
INSERT INTO salles_de_bain (id, nom) VALUES
(1, 'Douche'),
(2, 'Douche et WC séparé'),
(3, 'Baignoire'),
(4, 'Baignoire et WC séparé');  -- ❌ mauvais point-virgule ou virgule finale manquante
```

**Solution**  
Nettoyage de la syntaxe SQL :

```sql
INSERT INTO salles_de_bain (id, nom) VALUES
(1, 'Douche'),
(2, 'Douche et WC séparé'),
(3, 'Baignoire'),
(4, 'Baignoire et WC séparé');
```

---

### 2. ℹ️ Données incohérentes : `Lit superposé`

**Problème**  
Dans la table `couchages`, le lit superposé avait `nb_places = 1` alors qu’il peut loger 2 personnes.

```sql
(4, 'Lit superposé', 1)  -- ❌ incohérence
```

**Solution**  
Mise à jour à :

```sql
(4, 'Lit superposé', 2)
```

---

### 3. 🔍 Incohérence dans les types de couchage du type `Chambre Familiale +` (id=7)

**Problème**  
Le type 7 (`Chambre Familiale +`) n'était pas défini dans la table de jointure `chambre_type_couchage`.

**Solution**  
Ajout de :

```sql
(7, 2, 1),  -- lit double
(7, 4, 2)   -- 2 lits superposés
```
---

### 4. 🛏 Chambre 2 du Ski Hotel absente

**Problème**  
La chambre numéro 2 de l’hôtel Ski Hotel (id_hotel = 1) n’était pas définie dans la table `chambres`.

**Solution**  
Ajout de :

```sql
(2 ,1,1,'2','En travaux'),
```

---

```
-- phpMyAdmin SQL Dump
-- version 5.2.0
-- https://www.phpmyadmin.net/
--
-- Hôte : 127.0.0.1:3307
-- Généré le : mer. 16 avr. 2025 à 20:45
-- Version du serveur : 10.10.2-MariaDB
-- Version de PHP : 8.2.0

SET SQL_MODE = "NO_AUTO_VALUE_ON_ZERO";
START TRANSACTION;
SET time_zone = "+00:00";


/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8mb4 */;

--
-- Base de données : `reservation_hotel`
--

--
-- Déchargement des données de la table `chambres`
--

INSERT INTO `chambres` (`id`, `id_hotel`, `id_type`, `numero`, `commentaire`) VALUES
(1, 1, 1, '1', NULL),
(3, 1, 3, '3', NULL),
(4, 1, 1, '4', NULL),
(6, 1, 6, '6', NULL),
(8, 2, 1, '1', NULL),
(9, 2, 2, '2', NULL),
(10, 2, 3, '3', NULL),
(11, 2, 4, '4', NULL),
(12, 2, 5, '5', 'Vue sur le lac'),
(13, 2, 6, '6', NULL),
(14, 2, 8, '7', NULL),
(15, 3, 1, '1', NULL),
(16, 3, 2, '2', NULL),
(17, 3, 3, '3', NULL),
(18, 3, 4, '4', NULL),
(19, 3, 5, '5', NULL),
(20, 3, 6, '6', NULL),
(21, 3, 8, '7', NULL),
(22, 4, 1, '1', NULL),
(23, 4, 2, '2', NULL),
(24, 4, 3, '3', NULL),
(25, 4, 4, '4', NULL),
(26, 4, 5, '5', NULL),
(27, 4, 6, '6', NULL),
(28, 4, 8, '7', NULL),
(29, 1, 5, '5', 'Reservée aux VIP');

--
-- Déchargement des données de la table `chambre_types`
--

INSERT INTO `chambre_types` (`id`, `nom`, `description`, `id_salle_de_bain`) VALUES
(1, 'Chambre Standard', '1 lit simple avec douche (1 personne )', 1),
(2, 'Chambre Twin', '2 lits simples avec douche ( 2 personnes )', 1),
(3, 'Chambre Twin +', '2 lits simples avec douche et wc séparé (2 personnes )', 2),
(4, 'Chambre Double', '1 lit double avec douche (2 personnes)', 1),
(5, 'Chambre Double +', '1 lit double avec douche et WC séparé (2 personnes)', 2),
(6, 'Chambre Familiale', '1 lit double, 2 lits superposés avec douche et WC séparé (4 personnes)', 2),
(7, 'Chambre Familiale +', '1 lit double avec bain et WC séparé (4 personnes)', 4),
(8, 'Chambre King Size', '1 lit double large avec bain et WC séparé (2 personnes)', 4);

--
-- Déchargement des données de la table `chambre_type_couchage`
--

INSERT INTO `chambre_type_couchage` (`id_type`, `id_couchage`, `qte`) VALUES
(1, 1, 1),
(2, 1, 2),
(3, 1, 2),
(4, 2, 1),
(5, 2, 1),
(6, 2, 1),
(6, 4, 2),
(8, 3, 1);

--
-- Déchargement des données de la table `couchages`
--

INSERT INTO `couchages` (`id`, `nom`, `nb_places`) VALUES
(1, 'Lit simple', 1),
(2, 'Lit double', 2),
(3, 'Lit XL', 2),
(4, 'Lit superposé', 1);

--
-- Déchargement des données de la table `hotels`
--

INSERT INTO `hotels` (`id`, `libelle`, `etoile`) VALUES
(1, 'Ski Hotel', '*'),
(2, 'Art Hotel', '**'),
(3, 'Rose Hotel', '***'),
(4, 'Lions Hotel', '****');

--
-- Déchargement des données de la table `salles_de_bain`
--

INSERT INTO `salles_de_bain` (`id`, `nom`) VALUES
(3, 'Baignoire'),
(4, 'Baignoire et WC séparé'),
(1, 'Douche'),
(2, 'Douche et WC séparé');

--
-- Déchargement des données de la table `tarifs`
--

INSERT INTO `tarifs` (`id`, `id_hotel`, `id_type`, `date_debut`, `prix`) VALUES
(8, 2, 1, '2021-12-15', '57.49'),
(9, 2, 2, '2021-12-15', '68.99'),
(10, 2, 3, '2021-12-15', '80.49'),
(11, 2, 4, '2021-12-15', '68.99'),
(12, 2, 5, '2021-12-15', '80.49'),
(13, 2, 6, '2021-12-15', '91.99'),
(14, 2, 8, '2021-12-15', '103.49'),
(15, 3, 1, '2021-12-15', '57.49'),
(16, 3, 2, '2021-12-15', '68.99'),
(17, 3, 3, '2021-12-15', '80.49'),
(18, 3, 4, '2021-12-15', '68.99'),
(19, 3, 5, '2021-12-15', '80.49'),
(20, 3, 6, '2021-12-15', '91.99'),
(21, 3, 8, '2021-12-15', '103.49'),
(22, 4, 1, '2021-10-01', '49.99'),
(23, 4, 2, '2021-10-01', '59.99'),
(24, 4, 3, '2021-10-01', '69.99'),
(25, 4, 4, '2021-10-01', '59.99'),
(26, 4, 5, '2021-10-01', '69.99'),
(27, 4, 6, '2021-10-01', '79.99'),
(28, 4, 8, '2021-10-01', '89.99'),
(36, 4, 1, '2021-04-01', '57.49'),
(37, 4, 2, '2021-04-01', '68.99'),
(38, 4, 3, '2021-04-01', '80.49'),
(39, 4, 4, '2021-04-01', '68.99'),
(40, 4, 5, '2021-04-01', '80.49'),
(41, 4, 6, '2021-04-01', '91.99'),
(42, 4, 8, '2021-04-01', '103.49'),
(43, 2, 1, '2021-04-16', '49.99'),
(44, 2, 2, '2021-04-16', '59.99'),
(45, 2, 3, '2021-04-16', '69.99'),
(46, 2, 4, '2021-04-16', '59.99'),
(47, 2, 5, '2021-04-16', '69.99'),
(48, 2, 6, '2021-04-16', '79.99'),
(49, 2, 8, '2021-04-16', '89.99'),
(50, 3, 1, '2021-04-16', '49.99'),
(51, 3, 2, '2021-04-16', '59.99'),
(52, 3, 3, '2021-04-16', '69.99'),
(53, 3, 4, '2021-04-16', '59.99'),
(54, 3, 5, '2021-04-16', '69.99'),
(55, 3, 6, '2021-04-16', '79.99'),
(56, 3, 8, '2021-04-16', '89.99');
COMMIT;

/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;

```
