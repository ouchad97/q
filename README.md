# Activité MySQL Avancée — Plateforme Coach/Sportif

## Description

Cette activité vous propose de travailler sur une base de données pour une plateforme de gestion de coachs, sportifs, séances et réservations.  
Elle est conçue pour **tester vos compétences avancées en SQL** : jointures complexes, sous-requêtes corrélées, agrégations, fonctions analytiques, calculs temporels, et utilisation de `UNION`.

Vous utiliserez le script SQL ci-dessous pour créer la base, les tables, et insérer des données cohérentes permettant de réaliser les challenges.

---
✅ Instructions

Copier le script SQL et l’exécuter sur votre serveur MySQL.

Vérifier que les tables et les données sont créées correctement.

Résoudre chaque challenge en créant vos requêtes SQL.

Tester vos requêtes et analyser les résultats pour comprendre la logique métier.

Commiter vos requêtes et réponses dans le dépôt GitHub associé.
## Script SQL à utiliser

```sql

/* =====================================================
   DATABASE
===================================================== */
DROP DATABASE IF EXISTS coach_platform;
CREATE DATABASE coach_platform CHARACTER SET utf8mb4;
USE coach_platform;

/* =====================================================
   USERS (PARENT - HERITAGE)
===================================================== */
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(100),
    prenom VARCHAR(100),
    email VARCHAR(150) UNIQUE,
    role ENUM('coach', 'sportif') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

/* =====================================================
   COACHS (HERITAGE)
===================================================== */
CREATE TABLE coachs (
    user_id INT PRIMARY KEY,
    discipline VARCHAR(100),
    experience INT,
    description TEXT,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

/* =====================================================
   SPORTIFS (HERITAGE)
===================================================== */
CREATE TABLE sportifs (
    user_id INT PRIMARY KEY,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

/* =====================================================
   SEANCES
===================================================== */
CREATE TABLE seances (
    id INT AUTO_INCREMENT PRIMARY KEY,
    coach_id INT,
    date_seance DATE,
    heure TIME,
    duree INT, -- minutes
    statut ENUM('disponible', 'reservee') DEFAULT 'disponible',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (coach_id) REFERENCES coachs(user_id)
);

/* =====================================================
   RESERVATIONS
===================================================== */
CREATE TABLE reservations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    seance_id INT UNIQUE,
    sportif_id INT,
    reserved_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (seance_id) REFERENCES seances(id),
    FOREIGN KEY (sportif_id) REFERENCES sportifs(user_id)
);

/* =====================================================
   INSERT USERS
===================================================== */
INSERT INTO users (nom, prenom, email, role) VALUES
('El Amrani', 'Youssef', 'youssef@coach.com', 'coach'),
('Benali', 'Sara', 'sara@coach.com', 'coach'),
('Haddad', 'Karim', 'karim@coach.com', 'coach'),
('Ait Ali', 'Nadia', 'nadia@coach.com', 'coach'),
('Raji', 'Omar', 'omar@coach.com', 'coach'),

('Saidi', 'Amine', 'amine@sportif.com', 'sportif'),
('Lahcen', 'Rania', 'rania@sportif.com', 'sportif'),
('Fassi', 'Othmane', 'othmane@sportif.com', 'sportif'),
('Zahraoui', 'Salma', 'salma@sportif.com', 'sportif'),
('Kamal', 'Yassine', 'yassine@sportif.com', 'sportif'),
('Berrada', 'Imane', 'imane@sportif.com', 'sportif');

/* =====================================================
   INSERT COACHS
===================================================== */
INSERT INTO coachs (user_id, discipline, experience, description) VALUES
(1, 'Fitness', 8, 'Coach fitness certifié'),
(2, 'Yoga', 6, 'Spécialiste yoga et respiration'),
(3, 'Musculation', 10, 'Préparateur physique'),
(4, 'Pilates', 5, 'Coach pilates bien-être'),
(5, 'CrossFit', 7, 'CrossFit compétition');

/* =====================================================
   INSERT SPORTIFS
===================================================== */
INSERT INTO sportifs (user_id) VALUES
(6),(7),(8),(9),(10),(11);

/* =====================================================
   INSERT SEANCES
===================================================== */
INSERT INTO seances (coach_id, date_seance, heure, duree, statut) VALUES
(1, '2025-01-10', '10:00', 60, 'reservee'),
(1, '2025-01-11', '11:00', 90, 'reservee'),
(1, '2025-01-12', '10:30', 60, 'disponible'),

(2, '2025-02-05', '09:00', 60, 'reservee'),
(2, '2025-02-06', '09:30', 60, 'reservee'),
(2, '2025-02-07', '10:00', 60, 'disponible'),

-- Conflit horaire
(3, '2025-03-01', '14:00', 90, 'reservee'),
(3, '2025-03-01', '15:00', 60, 'reservee'),

-- Coach inactif
(4, '2024-11-01', '08:00', 60, 'disponible'),

(5, '2025-01-20', '18:00', 120, 'reservee'),
(5, '2025-01-22', '18:00', 120, 'reservee');

/* =====================================================
   INSERT RESERVATIONS
===================================================== */
INSERT INTO reservations (seance_id, sportif_id, reserved_at) VALUES
(1, 6, '2025-01-09 09:30'),
(2, 7, '2025-01-10 10:30'),
(4, 8, '2025-02-04 20:00'),
(5, 9, '2025-02-05 22:00'),
(7, 6, '2025-02-28 23:30'),
(8, 7, '2025-02-28 23:45'),
(10, 10, '2025-01-19 23:00'),
(11, 11, '2025-01-21 23:30');
```

---

## 🔥 Challenge 1 — Top coach par taux de réservation

Afficher pour chaque coach :

* nombre total de séances créées
* nombre de séances réservées
* taux de réservation (%)
* seulement les coachs ayant **≥3 séances**

**À utiliser :** `JOIN`, `COUNT`, `GROUP BY`, `HAVING`

---

## 🔥 Challenge 2 — Sportifs les plus actifs

Lister les sportifs qui ont réservé le **plus de séances par mois**, avec :

* nom, prénom
* nombre de réservations par mois
* mois et année
* ordre décroissant par nombre de réservations

**À utiliser :** `JOIN`, `GROUP BY`, `DATE_FORMAT`, `ORDER BY`

---

## 🔥 Challenge 3 — Détection de séances conflictuelles

Trouver les séances du **même coach** qui se **chevauchent dans le temps** :

* afficher coach, date, heure début, heure fin, id séance
* inclure toutes les séances conflictuelles

**À utiliser :** `SELF JOIN`, calcul `heure + duree`

---

## 🔥 Challenge 4 — Coachs inactifs mais avec sportifs actifs

Lister les coachs :

* qui n’ont aucune réservation depuis 60 jours
* mais dont les sportifs ont réservé des séances récemment

**À utiliser :** `LEFT JOIN`, `WHERE`, `DATEDIFF`, `GROUP BY`

---

## 🔥 Challenge 5 — Top 3 coachs par discipline

Pour chaque discipline, afficher :

* les 3 coachs avec le plus de séances réservées
* inclure le nombre de réservations

**À utiliser :** `JOIN`, `GROUP BY`, `RANK() OVER (PARTITION BY discipline ORDER BY COUNT(reservations.id) DESC)`

---

## 🔥 Challenge 6 — Sportifs opportunistes

Lister les sportifs :

* qui réservent toujours **moins de 24h avant la séance**
* avec le nombre de réservations correspondantes
* inclure le nom du coach

**À utiliser :** `JOIN`, `TIMESTAMPDIFF`, `GROUP BY`

---

## 🔥 Challenge 7 — Analyse des horaires populaires

Trouver les **plages horaires les plus réservées** :

* regrouper par `heure` ou tranche 1h
* afficher nombre de séances réservées
* ordre décroissant

**À utiliser :** `GROUP BY`, `FLOOR(HOUR(heure)/1)` ou `TIME_FORMAT`, `COUNT`

---

## 🔥 Challenge 8 — Union de coachs et sportifs

Créer une **liste combinée** :

* nom, prénom, rôle, nombre de séances ou réservations
* utiliser `UNION ALL` pour combiner coachs et sportifs
* inclure les valeurs null si pas de séances ou réservations

**À utiliser :** `LEFT JOIN`, `UNION ALL`, `GROUP BY`

---

## 🔥 Challenge 9 — Historique complet des réservations

Afficher pour chaque coach :

* toutes les séances
* avec tous les sportifs réservés (ou NULL si non réservé)
* inclure date de réservation, statut
* ordre par coach puis date

**À utiliser :** `LEFT JOIN`, `ORDER BY`, `COALESCE`

---

## 🔥 Challenge 10 — Taux de réservation par mois et discipline

Pour chaque mois et discipline :

* nombre total de séances
* nombre de séances réservées
* taux de réservation (%)
* ordre par mois puis taux décroissant

**À utiliser :** `JOIN`, `GROUP BY`, `DATE_FORMAT`, `SUM(CASE WHEN statut='reservee' THEN 1 ELSE 0 END)`

---
