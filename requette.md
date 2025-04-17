SELECT
    chambre_types.id,
    chambre_types.nom AS type,
    salles_de_bain.nom AS sanitaire
FROM
    chambres
JOIN
    chambre_types ON chambres.id_type = chambre_types.id
JOIN
    salles_de_bain ON chambre_types.id_salle_de_bain = salles_de_bain.id
GROUP BY
    chambre_types.id, chambre_types.nom, salles_de_bain.nom
