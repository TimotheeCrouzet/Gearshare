# GearShare

GearShare est une application de partage et de réservation de matériel entre étudiants. Un étudiant peut publier un objet, le consulter dans le catalogue et le réserver sur un créneau.

Le projet sert aussi à appliquer progressivement les concepts des TPs : Docker, API REST, tests, PostgreSQL, architecture en couches, migrations et authentification.

## Périmètre de départ

- Consulter publiquement le catalogue et la fiche d’un objet.
- Créer un compte et se connecter.
- Publier un objet ; seul son propriétaire peut le modifier ou le supprimer.
- Réserver un objet disponible sur des dates données.
- Consulter ses propres réservations et en annuler une.
- Refuser les réservations dont les dates sont invalides ou qui chevauchent une réservation active.

Les réservations représentent l’emprunt d’un objet. La messagerie, la négociation et le paiement ne font pas partie du périmètre défini pour le moment.

## Règles d’accès

- La lecture du catalogue et des fiches d’objets est publique.
- Les actions d’écriture demandent un utilisateur connecté.
- Seul le propriétaire peut modifier ou supprimer son objet.
- Les réservations sont des données personnelles : un utilisateur ne consulte que les siennes.
- Le propriétaire d’un objet ne peut pas agir sur un objet appartenant à un autre utilisateur.
- Le propriétaire peut-il annuler une réservation faite par un autre étudiant ? Cette règle reste à décider.

L’identité du propriétaire et de l’emprunteur doit venir de l’utilisateur authentifié, et non d’un identifiant fourni librement par le frontend.

## Modèle métier prévu

- **Utilisateur** : identifiant, email unique, nom affiché, mot de passe haché et date de création.
- **Objet** : propriétaire, titre, description facultative, tarif journalier, disponibilité et date de création.
- **Réservation** : objet, emprunteur, dates de début et de fin, statut (`active`, `annulee` ou `terminee`).

Le schéma des TPs impose un tarif journalier positif. Les dates doivent être cohérentes et une réservation active ne doit pas chevaucher une autre réservation active du même objet. La règle concernant deux créneaux jointifs reste à confirmer.

## Architecture et technologies

L’application comporte trois services conteneurisés :

1. **Frontend** — application Python séparée, FastAPI, HTML avec templates Jinja2 et CSS. Elle appelle le backend uniquement par son API.
2. **Backend** — FastAPI, API REST JSON et règles métier.
3. **Base de données** — PostgreSQL 17, accessible uniquement depuis le backend sur le réseau Docker.

Le backend suit les couches des TPs :

`router → service → repository → modèle SQLAlchemy → PostgreSQL`

La stack prévue est Python 3.12+, FastAPI, SQLAlchemy 2, Alembic, PostgreSQL 17, Docker Compose et pytest. Le démarrage doit se faire avec `docker compose up`, avec migrations appliquées automatiquement. La configuration et les secrets sont fournis par variables d’environnement ; seul `.env.example` est versionné, jamais `.env`.

## Concepts des TPs à appliquer

- Définir le contrat REST : routes, méthodes HTTP, schémas Pydantic, codes de statut et erreurs.
- Tester les cas nominaux, les validations, les absences, les conflits métier et les permissions avec pytest.
- Garantir l’intégrité en base par les clés, contraintes, transactions et index appropriés.
- Versionner les évolutions du schéma avec Alembic et relire les migrations avant de les appliquer.
- Garder les règles métier dans les services, les requêtes dans les repositories et la traduction HTTP dans les routers.
- Éviter les fuites de champs privés et le problème de requêtes N+1.
- Vérifier que l’application démarre depuis un clone propre et que les données survivent au redémarrage grâce au volume PostgreSQL.
- Documenter les décisions, relire les changements générés et commiter régulièrement.

## Décisions à préciser

- Qui peut annuler une réservation : seulement l’emprunteur ou aussi le propriétaire de l’objet ?
- Les créneaux de réservation qui se touchent sans se chevaucher sont-ils autorisés ?
- Quelles routes du catalogue et des fiches sont publiques ? Par défaut, elles le sont.

Ces choix pourront être ajustés en fonction du sujet détaillé et des consignes des séances suivantes.
