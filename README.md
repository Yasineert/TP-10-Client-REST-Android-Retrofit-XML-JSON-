# 🏦 Gestion de Comptes Bancaires - Application Android

## 📋 Description du Projet

Application Android complète permettant de gérer des comptes bancaires via une API REST. Le projet implémente toutes les opérations CRUD (Create, Read, Update, Delete) avec support des formats JSON et XML.

### 🎯 Objectifs Pédagogiques

- Consommer un service REST depuis une application Android
- Utiliser Retrofit pour les appels API
- Implémenter RecyclerView pour l'affichage des données
- Gérer les conversions JSON/XML
- Appliquer l'architecture en couches
- Utiliser Material Design pour l'interface utilisateur

## 🏗️ Architecture du Projet

```
TP-10-Client-REST-Android-Retrofit-XML-JSON/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/ma/projet/restclient/
│   │   │   │   ├── MainActivity.java           # Activité principale
│   │   │   │   ├── entities/                   # Modèles de données
│   │   │   │   │   ├── Compte.java
│   │   │   │   │   └── CompteList.java
│   │   │   │   ├── api/                        # Interfaces API
│   │   │   │   │   └── CompteService.java
│   │   │   │   ├── config/                     # Configuration Retrofit
│   │   │   │   │   └── RetrofitClient.java
│   │   │   │   ├── repository/                 # Couche d'accès aux données
│   │   │   │   │   └── CompteRepository.java
│   │   │   │   └── adapter/                    # Adaptateurs RecyclerView
│   │   │   │       └── CompteAdapter.java
│   │   │   ├── res/                            # Ressources
│   │   │   │   ├── layout/
│   │   │   │   │   ├── activity_main.xml
│   │   │   │   │   ├── item_compte.xml
│   │   │   │   │   └── dialog_add_compte.xml
│   │   │   │   └── values/
│   │   │   └── AndroidManifest.xml
│   │   └── test/                               # Tests unitaires
│   └── build.gradle
├── gradle/
├── build.gradle
└── settings.gradle
```

## 🚀 Technologies Utilisées

### Frameworks & Bibliothèques

- **Android SDK 34** (compileSdk)
- **Min SDK 24** (Android 7.0 Nougat)
- **Retrofit 2.9.0** - Client HTTP REST
- **Gson 2.9.0** - Sérialization/Désérialisation JSON
- **SimpleXML 2.7.1** - Parsing XML
- **Material Components 1.10.0** - Interface utilisateur
- **RecyclerView** - Affichage des listes
- **ConstraintLayout 2.1.4** - Layouts flexibles

### Langage

- **Java 8** (sourceCompatibility & targetCompatibility)
- **Kotlin** (support pour les tests)

## 📱 Fonctionnalités

### ✅ Opérations CRUD

1. **CREATE** - Ajouter un nouveau compte
   - Saisie du solde initial
   - Choix du type de compte (COURANT/EPARGNE)
   - Date de création automatique

2. **READ** - Afficher la liste des comptes
   - Support JSON et XML (sélection dynamique)
   - Affichage via RecyclerView
   - Actualisation automatique

3. **UPDATE** - Modifier un compte existant
   - Modification du solde
   - Changement du type de compte
   - Validation des données

4. **DELETE** - Supprimer un compte
   - Confirmation avant suppression
   - Actualisation de la liste

### 🎨 Interface Utilisateur

- Design Material (Material Design 3)
- FloatingActionButton pour l'ajout
- AlertDialogs pour les formulaires
- RadioButtons pour sélection JSON/XML
- Indicateurs visuels et feedbacks utilisateur

## 🔧 Configuration & Installation

### Prérequis

- Android Studio (version récente recommandée)
- JDK 8 ou supérieur
- Émulateur Android ou appareil physique (Android 7.0+)
- Backend REST API (Spring Boot recommandé) sur le port 8082

### Installation

1. **Cloner ou télécharger le projet**
   ```
   Le projet est prêt à être ouvert dans Android Studio
   ```

2. **Ouvrir dans Android Studio**
   - File → Open
   - Sélectionner le dossier du projet
   - Attendre la synchronisation Gradle

3. **Configurer le Backend**
   - L'application se connecte à : `http://10.0.2.2:8082/api/comptes`
   - `10.0.2.2` = localhost pour l'émulateur Android
   - Pour un appareil physique, remplacer par l'adresse IP de votre machine

4. **Modifier l'URL si nécessaire**
   - Fichier : `app/src/main/java/ma/projet/restclient/config/RetrofitClient.java`
   - Ligne : `private static final String BASE_URL = "http://10.0.2.2:8082/";`

5. **Exécuter l'application**
   - Run → Run 'app'
   - Sélectionner un émulateur ou appareil

## 🌐 API REST Endpoints

L'application consomme les endpoints suivants :

| Méthode | Endpoint | Description | Format |
|---------|----------|-------------|--------|
| GET | `/api/comptes` | Récupérer tous les comptes | JSON/XML |
| GET | `/api/comptes/{id}` | Récupérer un compte par ID | JSON |
| POST | `/api/comptes` | Créer un nouveau compte | JSON |
| PUT | `/api/comptes/{id}` | Mettre à jour un compte | JSON |
| DELETE | `/api/comptes/{id}` | Supprimer un compte | - |

### Format des Données

**Objet Compte (JSON)**
```json
{
  "id": 1,
  "solde": 5000.0,
  "type": "COURANT",
  "dateCreation": "2025-11-26"
}
```

**Liste Comptes (XML)**
```xml
<List>
  <item>
    <id>1</id>
    <solde>5000.0</solde>
    <type>COURANT</type>
    <dateCreation>2025-11-26</dateCreation>
  </item>
</List>
```

## 📚 Structure du Code

### Couche Entités (entities/)

**Compte.java** - Modèle de données principal
- Propriétés : id, solde, type, dateCreation
- Annotations XML (@Root, @Element) pour parsing XML
- Annotations JAXB (@XmlElement) pour compatibilité

**CompteList.java** - Wrapper pour désérialisation XML
- Contient une liste de comptes
- Utilisé pour la réponse XML

### Couche API (api/)

**CompteService.java** - Interface Retrofit
- Définit tous les endpoints REST
- Headers pour spécifier JSON ou XML
- Méthodes asynchrones avec Call<>

### Couche Configuration (config/)

**RetrofitClient.java** - Singleton Retrofit
- Gère la création de l'instance Retrofit
- Convertisseurs dynamiques (Gson/SimpleXML)
- Configuration de l'URL de base

### Couche Repository (repository/)

**CompteRepository.java** - Abstraction des appels API
- Méthodes pour toutes les opérations CRUD
- Gestion du format (JSON/XML)
- Callbacks pour la gestion asynchrone

### Couche Présentation

**MainActivity.java** - Activité principale
- Gestion du RecyclerView
- Création des dialogs
- Gestion des événements utilisateur
- Actualisation des données

**CompteAdapter.java** - Adaptateur RecyclerView
- Pattern ViewHolder
- Interfaces pour les actions (Update/Delete)
- Binding des données aux vues

## 🎓 Concepts Android Mis en Œuvre

### Architecture
- **Séparation des responsabilités** (MVC pattern)
- **Repository Pattern** pour l'accès aux données
- **Adapter Pattern** pour RecyclerView

### Composants Android
- **Activity** - Point d'entrée de l'application
- **RecyclerView** - Affichage efficace des listes
- **AlertDialog** - Dialogs modaux
- **FloatingActionButton** - Action principale

### Réseau
- **Retrofit** - Client HTTP type-safe
- **Callbacks asynchrones** - Éviter le blocage UI
- **Convertisseurs** - Gson et SimpleXML
- **Permissions Internet** - AndroidManifest.xml

### Interface
- **Material Design** - Components modernes
- **ConstraintLayout** - Layouts flexibles
- **Material Dialogs** - Expérience utilisateur cohérente

## 🔒 Sécurité & Permissions

### Permissions Requises

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### Configuration Réseau

- `network_security_config.xml` - Autorise le trafic HTTP en développement
- Configuration de sécurité pour Android 9+

## 🐛 Dépannage

### Problème de Connexion

**Symptôme** : L'application ne peut pas se connecter au backend

**Solutions** :
1. Vérifier que le backend est en cours d'exécution sur le port 8082
2. Pour émulateur : utiliser `10.0.2.2` au lieu de `localhost`
3. Pour appareil physique : utiliser l'adresse IP locale de votre machine
4. Vérifier le firewall Windows/pare-feu

### Erreur de Parsing

**Symptôme** : Erreur lors de la conversion JSON/XML

**Solutions** :
1. Vérifier le format des données retournées par l'API
2. S'assurer que les annotations correspondent au format
3. Activer les logs Retrofit pour déboguer

### Problème de Build

**Symptôme** : Erreurs de compilation Gradle

**Solutions** :
1. File → Invalidate Caches / Restart
2. Supprimer les dossiers `.gradle` et `.idea`
3. Re-synchroniser le projet Gradle

## 📸 Captures d'écran

### Backend Spring
<img width="801" height="515" alt="Backend" src="https://github.com/user-attachments/assets/9cf02500-27eb-47de-be3a-0cccaba39e90" />


### Ajouter un Compte

<img width="761" height="505" alt="ajouter" src="https://github.com/user-attachments/assets/afefa792-0d8d-4df5-bafb-dc0b142496b8" />

### Modifier un Compte
<img width="765" height="515" alt="modifier" src="https://github.com/user-attachments/assets/458d3bbf-e580-4f7a-b75e-22245afedbfe" />


### Supprimer un Compte
<img width="753" height="507" alt="supprimer" src="https://github.com/user-attachments/assets/b73541ed-6b6c-4f59-b938-9e826006d6fc" />



---

**Date de création** : Novembre 2025  
**Version** : 1.0  
**Android Min SDK** : 24 (Android 7.0)  
**Android Target SDK** : 34 (Android 14)
