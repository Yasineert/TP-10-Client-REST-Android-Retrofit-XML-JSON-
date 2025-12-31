# 📱 Application Module - Documentation

## 📋 Vue d'Ensemble

Ce module contient le code source principal de l'application Android de gestion de comptes bancaires. Il implémente toutes les couches nécessaires pour une application REST client complète.

## 🏗️ Structure du Module

```
app/
├── src/
│   ├── main/
│   │   ├── java/ma/projet/restclient/
│   │   │   ├── MainActivity.java              # Point d'entrée de l'application
│   │   │   ├── entities/                      # 📦 Couche Modèle
│   │   │   ├── api/                           # 🌐 Couche Service
│   │   │   ├── config/                        # ⚙️ Configuration
│   │   │   ├── repository/                    # 💾 Couche Données
│   │   │   └── adapter/                       # 🎨 Couche Présentation
│   │   ├── res/                               # 🎨 Ressources UI
│   │   │   ├── layout/                        # Layouts XML
│   │   │   ├── drawable/                      # Images et icônes
│   │   │   ├── values/                        # Styles, couleurs, strings
│   │   │   └── xml/                           # Configurations XML
│   │   └── AndroidManifest.xml                # Manifest de l'application
│   ├── androidTest/                           # Tests d'instrumentation
│   └── test/                                  # Tests unitaires
├── build.gradle                               # Configuration Gradle
└── proguard-rules.pro                         # Règles ProGuard
```

## 📦 Couche Entités (entities/)

### Compte.java
**Rôle** : Modèle de données représentant un compte bancaire

**Propriétés** :
- `id` (Long) : Identifiant unique du compte
- `solde` (double) : Solde du compte
- `type` (String) : Type de compte (COURANT/EPARGNE)
- `dateCreation` (String) : Date de création au format ISO

**Annotations** :
- `@Root` : Annotation SimpleXML pour parsing XML
- `@Element` : Mapping des champs XML
- `@XmlElement` : Annotations JAXB pour compatibilité

**Exemple d'utilisation** :
```java
Compte compte = new Compte(null, 5000.0, "COURANT", "2025-11-26");
```

### CompteList.java
**Rôle** : Wrapper pour la désérialisation XML des listes

**Structure** :
```xml
<List>
  <item><!-- Compte 1 --></item>
  <item><!-- Compte 2 --></item>
</List>
```

**Usage** : Utilisé par Retrofit pour convertir les réponses XML en objets Java

## 🌐 Couche API (api/)

### CompteService.java
**Rôle** : Interface Retrofit définissant tous les endpoints REST

**Méthodes** :

#### GET - Récupérer tous les comptes
```java
@GET("api/comptes")
@Headers("Accept: application/json")
Call<List<Compte>> getAllCompteJson();

@GET("api/comptes")
@Headers("Accept: application/xml")
Call<CompteList> getAllCompteXml();
```

#### GET - Récupérer un compte par ID
```java
@GET("api/comptes/{id}")
Call<Compte> getCompteById(@Path("id") Long id);
```

#### POST - Créer un nouveau compte
```java
@POST("api/comptes")
Call<Compte> addCompte(@Body Compte compte);
```

#### PUT - Mettre à jour un compte
```java
@PUT("api/comptes/{id}")
Call<Compte> updateCompte(@Path("id") Long id, @Body Compte compte);
```

#### DELETE - Supprimer un compte
```java
@DELETE("api/comptes/{id}")
Call<Void> deleteCompte(@Path("id") Long id);
```

## ⚙️ Couche Configuration (config/)

### RetrofitClient.java
**Rôle** : Singleton pour gérer l'instance Retrofit

**Caractéristiques** :
- Pattern Singleton
- Configuration dynamique JSON/XML
- Base URL : `http://10.0.2.2:8082/`

**Convertisseurs** :
- **JSON** : GsonConverterFactory
- **XML** : SimpleXmlConverterFactory (non-strict)

**Méthode principale** :
```java
public static Retrofit getClient(String converterType)
```

**Utilisation** :
```java
// Pour JSON
Retrofit retrofit = RetrofitClient.getClient("JSON");

// Pour XML
Retrofit retrofit = RetrofitClient.getClient("XML");
```

## 💾 Couche Repository (repository/)

### CompteRepository.java
**Rôle** : Abstraction de la couche d'accès aux données

**Responsabilités** :
- Encapsulation des appels API
- Gestion du format (JSON/XML)
- Conversion des réponses
- Gestion des callbacks

**Méthodes** :

#### getAllCompte()
Récupère tous les comptes selon le format configuré
```java
public void getAllCompte(Callback<List<Compte>> callback)
```

#### getCompteById()
Récupère un compte spécifique
```java
public void getCompteById(Long id, Callback<Compte> callback)
```

#### addCompte()
Ajoute un nouveau compte
```java
public void addCompte(Compte compte, Callback<Compte> callback)
```

#### updateCompte()
Met à jour un compte existant
```java
public void updateCompte(Long id, Compte compte, Callback<Compte> callback)
```

#### deleteCompte()
Supprime un compte
```java
public void deleteCompte(Long id, Callback<Void> callback)
```

## 🎨 Couche Présentation (adapter/)

### CompteAdapter.java
**Rôle** : Adaptateur RecyclerView pour afficher la liste des comptes

**Pattern** : ViewHolder Pattern pour optimiser les performances

**Interfaces** :
```java
public interface OnDeleteClickListener {
    void onDeleteClick(Compte compte);
}

public interface OnUpdateClickListener {
    void onUpdateClick(Compte compte);
}
```

**Méthodes principales** :
- `onCreateViewHolder()` : Crée les ViewHolders
- `onBindViewHolder()` : Lie les données aux vues
- `updateData()` : Actualise la liste complète

**ViewHolder** :
- Contient les références aux vues (TextViews, Buttons)
- Gère les clics sur les boutons
- Formate l'affichage des données

## 📱 MainActivity.java

### Responsabilités

1. **Gestion du Cycle de Vie**
   - Initialisation des composants
   - Configuration du RecyclerView
   - Setup des listeners

2. **Gestion de l'UI**
   - Affichage de la liste des comptes
   - Sélection du format (JSON/XML)
   - Création et affichage des dialogs

3. **Opérations CRUD**
   - Ajout de comptes (dialog + appel API)
   - Modification de comptes (dialog + appel API)
   - Suppression de comptes (confirmation + appel API)
   - Chargement de la liste

4. **Gestion des Événements**
   - Clic sur RadioButton (JSON/XML)
   - Clic sur FloatingActionButton (ajout)
   - Clic sur boutons Modifier/Supprimer

### Méthodes Principales

#### initViews()
Initialise les références aux vues
```java
private void initViews()
```

#### setupRecyclerView()
Configure le RecyclerView avec son adapter
```java
private void setupRecyclerView()
```

#### loadData(String format)
Charge les données depuis l'API
```java
private void loadData(String format)
```

#### showAddCompteDialog()
Affiche le dialog d'ajout de compte
```java
private void showAddCompteDialog()
```

#### showUpdateCompteDialog(Compte compte)
Affiche le dialog de modification
```java
private void showUpdateCompteDialog(Compte compte)
```

#### showDeleteConfirmationDialog(Compte compte)
Affiche la confirmation de suppression
```java
private void showDeleteConfirmationDialog(Compte compte)
```

## 🎨 Ressources (res/)

### Layouts

#### activity_main.xml
**Composants** :
- CoordinatorLayout (conteneur principal)
- MaterialCardView (sélection JSON/XML)
- RadioGroup (JSON/XML)
- RecyclerView (liste des comptes)
- FloatingActionButton (ajout)

#### item_compte.xml
**Composants** :
- ConstraintLayout
- 4 TextViews (ID, Solde, Type, Date)
- 2 Buttons (Modifier, Supprimer)

#### dialog_add_compte.xml
**Composants** :
- TextInputLayout + TextInputEditText (Solde)
- MaterialCardView
- RadioGroup (Type de compte)
- MaterialRadioButtons (COURANT/EPARGNE)

### Values

#### strings.xml
Contient tous les textes de l'application
```xml
<string name="app_name">RestClient</string>
<!-- Autres strings -->
```

#### colors.xml
Définit la palette de couleurs
```xml
<color name="design_default_color_primary">#6200EE</color>
<!-- Autres couleurs -->
```

#### themes.xml
Définit le thème de l'application
```xml
<style name="Theme.Restclient" parent="Theme.MaterialComponents.DayNight">
    <!-- Configuration du thème -->
</style>
```

### XML

#### network_security_config.xml
Configure la sécurité réseau pour autoriser HTTP en développement
```xml
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```

## 📝 AndroidManifest.xml

### Configuration Principale

**Permissions** :
```xml
<uses-permission android:name="android.permission.INTERNET" />
```

**Application** :
- `networkSecurityConfig` : Configuration réseau
- `theme` : Thème Material
- Activité principale : MainActivity

**Intent Filters** :
- MAIN : Point d'entrée
- LAUNCHER : Icône sur l'écran d'accueil

## 🔧 build.gradle (Module: app)

### Configuration Android

```gradle
android {
    namespace 'ma.projet.restclient'
    compileSdk 34
    
    defaultConfig {
        applicationId "ma.projet.restclient"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }
}
```

### Dépendances Principales

**Retrofit & Convertisseurs** :
```gradle
implementation "com.squareup.retrofit2:retrofit:2.9.0"
implementation "com.squareup.retrofit2:converter-gson:2.9.0"
implementation "com.squareup.retrofit2:converter-simplexml:2.9.0"
```

**SimpleXML** :
```gradle
implementation "org.simpleframework:simple-xml:2.7.1"
```

**Material Components** :
```gradle
implementation "com.google.android.material:material:1.10.0"
```

**RecyclerView** :
```gradle
implementation libs.androidx.recyclerview
```

## 🎯 Points d'Attention

### Sécurité
- ⚠️ HTTP non chiffré (développement uniquement)
- 🔒 En production, utiliser HTTPS
- 🔐 Ajouter authentification si nécessaire

### Performance
- ✅ ViewHolder Pattern (RecyclerView optimisé)
- ✅ Appels asynchrones (pas de blocage UI)
- ⚠️ Pas de cache (recharge à chaque fois)

### Best Practices Implémentées
- ✅ Séparation des responsabilités
- ✅ Repository Pattern
- ✅ Adapter Pattern
- ✅ Callbacks pour asynchrone
- ✅ Material Design

### Améliorations Possibles
- 💡 Ajouter Room Database (cache local)
- 💡 Implémenter LiveData/ViewModel (MVVM)
- 💡 Ajouter gestion d'erreurs robuste
- 💡 Implémenter la pagination
- 💡 Ajouter des animations
- 💡 Tests unitaires et d'intégration

## 📚 Ressources Utiles

### Documentation
- [Retrofit](https://square.github.io/retrofit/)
- [Material Design](https://material.io/develop/android)
- [RecyclerView](https://developer.android.com/guide/topics/ui/layout/recyclerview)
- [Android Developer Guides](https://developer.android.com/guide)

### Tutoriels
- Retrofit avec Kotlin/Java
- RecyclerView avancé
- Material Design Components
- Architecture Android (MVVM, MVP)

---

**Version du module** : 1.0  
