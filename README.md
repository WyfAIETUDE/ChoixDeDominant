# ChoixDeDominant
# Système de Choix de Dominantes ESIGELEC - Documentation complète

## Vue d'ensemble du système

Ce système est une plateforme développée par l'ESIGELEC pour permettre aux étudiants de 2ème année de choisir leurs dominantes pour les 3 derniers semestres de formation lors du premier semestre de la 2ème année.

## Architecture du système

### 1. Couche Modèle de données (Model)
- **Administrateur**: Entité administrateur
- **Authentification**: Entité informations d'authentification
- **Choixdedominante**: Enregistrement des choix de dominantes
- **Dominante**: Entité dominante
- **Etudiant**: Entité étudiant
- **Promotion**: Entité promotion/classe
- **AutoAllocator**: Algorithme d'affectation automatique

### 2. Couche Accès aux données (DAO)
- **ConnectionDAO**: Classe de base de connexion à la base de données
- **AdministrateurDAO**: Opérations sur les administrateurs
- **AttributionDAO**: Traitement des résultats d'affectation
- **ChoixdedominanteDAOG**: Opérations sur les choix
- **DominanteDAO**: Opérations sur les dominantes
- **EtudiantDAO**: Opérations sur les étudiants
- **PromotionDAO**: Opérations sur les promotions
- **AuthentificationDAO**: Gestion de l'authentification

### 3. Couche Interface Utilisateur (GUI)
- **AdministrateurGUI**: Interface principale administrateur
- **DominanteGUI**: Interface de gestion des dominantes
- **EtudiantGUI**: Interface de choix des étudiants
- **PromotionGUI**: Interface de gestion des promotions
- **StatutPlacesAppGUI**: État des places apprentissage
- **StatutPlacesClassiqueGUI**: État des places classiques

## Processus métier

### 1. Phase de préparation par l'administrateur
- Configuration des dominantes (nom/sigle/nombre total de places/places apprentissage)
- Création des promotions et définition de leur état initial
- Import des données étudiants (nom/date de naissance/classement/type)

### 2. Phase de choix des étudiants
| Type d'étudiant | Nombre de choix | État d'ouverture |
|----------------|----------------|------------------|
| Apprentissage  | 2 choix | "Ouvert aux apprentis" |
| Classique      | 5 choix | "Ouvert aux classiques" |

### 3. Phase d'affectation automatique
1. Processus d'affectation apprentissage :
   - État passe à "Fermé aux apprentis"
   - Priorité donnée selon le classement de 1ère année
   - Prise en compte des quotas apprentissage
   - État passe à "Validé apprentis"

2. Processus d'affectation classique :
   - État passe à "Fermé aux classiques"
   - Priorité selon le classement et les préférences
   - Prise en compte des places restantes
   - État passe à "Validés classiques"

### 4. Phase d'archivage
- État passe à "Archivé"
- Données en lecture seule

## Caractéristiques techniques

1. **Algorithme d'affectation** :
```java
// Logique centrale d'AutoAllocator
etudiants.sort(Comparator.comparingInt(Etudiant::getClassement));
for(Etudiant e : etudiants) {
    for(Dominante d : e.getChoixDominantes()) {
        if(quotaRestant > 0) {
            // Logique d'affectation
        }
    }
}
```

2. **Gestion des états** :
```java
// Liste des états de Promotion
String[] etats = {
    "En préparation",
    "Ouvert aux apprentis", 
    "Fermé aux apprentis",
    "Validé apprentis",
    "Ouvert aux classiques",
    "Fermé aux classiques",
    "Validés classiques",
    "Archivé"
};
```

3. **Validation des données** :
```java
// Validation des choix étudiants
public boolean peutAccepter() {
    return quota > 0;
}
```

## Guide de déploiement

### Prérequis
- Java 8+
- Base de données Oracle
- Pilote JDBC (ojdbc8.jar)

### Étapes de configuration
1. Modifier les paramètres de connexion dans `ConnectionDAO.java` :
```java
final static String URL = "jdbc:oracle:thin:@oracle.esigelec.fr:1521:orcl";
final static String LOGIN = "C##BDD8_14";
final static String PASS = "BDD814";
```

2. Exécuter le script SQL pour créer la structure de la base

3. Démarrer le système :
```bash
java -jar ESIGELEC-ChoixDominante.jar
```

## Manuel d'utilisation

### Fonctionnalités administrateur
1. Gestion des dominantes :
   - Ajout/modification/suppression
   - Définition des quotas

2. Gestion des étudiants :
   - Consultation par promotion
   - Modification des informations
   - Réinitialisation des mots de passe

3. Contrôle du processus :
   - Changement d'état des promotions
   - Déclenchement de l'affectation
   - Consultation des résultats

### Fonctionnalités étudiant
1. Consultation des dominantes disponibles
2. Soumission des choix par ordre de préférence
3. Consultation des résultats d'affectation

## FAQ

Q: Comment réinitialiser un mot de passe étudiant ?
R: Dans PromotionGUI, sélectionner l'étudiant et cliquer "Modifier code"

Q: Comment l'algorithme assure-t-il l'équité ?
R: Traitement strict par ordre de classement, priorité aux meilleurs étudiants

Q: Comment sauvegarder les données ?
R: Exporter régulièrement les tables :
- DOMINANTE
- ETUDIANT
- CHOIXDOMINANTE
- PROMOTION

## Contact

Équipe de maintenance : yangzhen@groupe-esigelec.org / yufan.wu@groupe-esigelec.fr

Version : 2.0.0
Dernière mise à jour : 15 juillet 2025
