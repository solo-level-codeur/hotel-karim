# Suppression en cascade

## Essai de suppression

Essayez de supprimer une entrée dans la table `hotels`.

```SQL
DELETE FROM hotels WHERE hotels.id = 1
```

Avec RESTRICT, la suppression échoue :

```SQL
#1451 - Impossible de supprimer ou de mettre à jour une ligne parente : une contrainte de clé étrangère échoue (`reservation_hotel`.`chambres`, CONSTRAINT `chambres_ibfk_2` FOREIGN KEY (`id_hotel`) REFERENCES `hotels` (`id`) ON DELETE RESTRICT ON UPDATE CASCADE)
```

## Changement des contraintes

Modifiez les contraintes pour utiliser ON DELETE CASCADE.

Maintenant, cette requête fonctionne :

```SQL
DELETE FROM hotels WHERE hotels.id = 1
```

L'hôtel et les chambres associées sont supprimés.

## Test de la mise à jour en cascade

Si vous changez l'id de l'hôtel 2 en 20, les chambres associées seront mises à jour.

```SQL
UPDATE hotels SET id = 20 WHERE id = 2;
```

id, id_hotel, id_type, numero, commentaire
'1', 20, '1', '1', 'NB : En travaux'
'3', 20, '3', '3', NULL
'4', 20, '1', '4', NULL
'6', 20, '6', '6', NULL

## Test de la suppression avec NULL

Si vous supprimez un hôtel, les chambres associées auront leur `id_hotel` mis à NULL.

id, id_hotel, id_type, numero, commentaire
'1', NULL, '1', '1', 'NB : En travaux'
'3', NULL, '3', '3', NULL
'4', NULL, '1', '4', NULL
'6', NULL, '6', '6', NULL

## Contraintes CASCADE et RESTRICT

Si `chambres` et `hôtels` utilisent CASCADE et `hôtels` et `tarifs` utilisent RESTRICT :

### Analyse

- **Cascade entre chambres et hôtels** : Supprimer un hôtel supprime aussi les chambres associées.
- **Restriction entre hôtels et tarifs** : Impossible de supprimer un hôtel référencé dans `tarifs`.

### Conclusion

Vous ne pouvez supprimer un hôtel que s'il n'est pas référencé dans `tarifs`. Sinon, la suppression échoue.

```SQL
DELETE FROM hotels WHERE hotels.id = 1;

MySQL a répondu :

#1451 - Impossible de supprimer ou de mettre à jour une ligne parente : une contrainte de clé étrangère échoue (`reservation_hotel`.`tarifs`, CONSTRAINT `tarifs_ibfk_1` FOREIGN KEY (`id_hotel`) REFERENCES `hotels` (`id`) ON DELETE RESTRICT ON UPDATE CASCADE)
```
