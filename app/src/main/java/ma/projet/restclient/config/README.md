# ⚙️ Package Config - Configuration Retrofit

## 📋 Description

Ce package contient les classes de configuration pour la communication réseau. Il gère la création et la configuration de l'instance Retrofit utilisée dans toute l'application.

## 📁 Classes

### RetrofitClient.java
**Rôle** : Singleton pour gérer l'instance Retrofit

## 🏗️ Architecture Singleton

### Principe
Une seule instance de Retrofit est créée et réutilisée dans toute l'application, évitant ainsi :
- La création multiple d'objets coûteux
- La surcharge mémoire
- Les connexions réseau inutiles

### Implémentation

```java
public class RetrofitClient {
    private static Retrofit retrofit = null;
    private static String currentFormat = null;
    private static final String BASE_URL = "http://10.0.2.2:8082/";
    
    public static Retrofit getClient(String converterType) {
        // Logique de création/réutilisation
    }
}
```

## 🔧 Propriétés

### BASE_URL
```java
private static final String BASE_URL = "http://10.0.2.2:8082/";
```

**Description** : URL de base du backend

**Valeurs possibles** :
- `http://10.0.2.2:8082/` : Pour émulateur Android (pointe vers localhost)
- `http://192.168.x.x:8082/` : Pour appareil physique (IP locale)
- `https://api.example.com/` : Pour production

### retrofit
```java
private static Retrofit retrofit = null;
```

**Description** : Instance singleton de Retrofit

### currentFormat
```java
private static String currentFormat = null;
```

**Description** : Format actuel du convertisseur (JSON ou XML)

## 📍 Méthode Principale

### getClient(String converterType)

```java
public static Retrofit getClient(String converterType)
```

**Description** : Retourne l'instance Retrofit configurée avec le convertisseur approprié

**Paramètres** :
- `converterType` (String) : "JSON" ou "XML"

**Retour** : Instance de Retrofit configurée

**Logique** :
1. Vérifie si une instance existe avec le bon convertisseur
2. Si non, crée une nouvelle instance avec le convertisseur demandé
3. Retourne l'instance

### Flux de Décision

```
getClient("JSON")
    ↓
Retrofit existe ?
    ↓ Non
Créer Retrofit.Builder
    ↓
Format = JSON ?
    ↓ Oui
Ajouter GsonConverterFactory
    ↓
Construire Retrofit
    ↓
Retourner instance
```

## 🔄 Convertisseurs

### JSON - Gson

```java
if ("JSON".equals(converterType)) {
    builder.addConverterFactory(GsonConverterFactory.create());
}
```

**Utilisation** :
- Conversion automatique JSON ↔ Java
- Basé sur Gson de Google
- Configuration par défaut

**Exemple** :
```json
{"id": 1, "solde": 5000.0}
```
↓↓↓
```java
Compte{id=1, solde=5000.0}
```

### XML - SimpleXML

```java
else if ("XML".equals(converterType)) {
    builder.addConverterFactory(SimpleXmlConverterFactory.createNonStrict());
}
```

**Utilisation** :
- Conversion automatique XML ↔ Java
- Mode non-strict pour plus de flexibilité
- Basé sur SimpleXML

**Exemple** :
```xml
<item>
  <id>1</id>
  <solde>5000.0</solde>
</item>
```
↓↓↓
```java
Compte{id=1, solde=5000.0}
```

## 💻 Utilisation

### Obtenir une Instance JSON

```java
Retrofit retrofit = RetrofitClient.getClient("JSON");
CompteService service = retrofit.create(CompteService.class);
```

### Obtenir une Instance XML

```java
Retrofit retrofit = RetrofitClient.getClient("XML");
CompteService service = retrofit.create(CompteService.class);
```

### Exemple Complet

```java
// Dans CompteRepository
public CompteRepository(String converterType) {
    compteService = RetrofitClient.getClient(converterType)
                        .create(CompteService.class);
    this.format = converterType;
}
```

## 🌐 Configuration Réseau

### URLs selon l'Environnement

#### Émulateur Android
```java
private static final String BASE_URL = "http://10.0.2.2:8082/";
```
- `10.0.2.2` = Alias pour localhost depuis l'émulateur
- Port 8082 = Port du backend Spring Boot

#### Appareil Physique
```java
private static final String BASE_URL = "http://192.168.1.100:8082/";
```
- Remplacer par l'adresse IP locale de votre machine
- Les deux appareils doivent être sur le même réseau

#### Production
```java
private static final String BASE_URL = "https://api.monapp.com/";
```
- Utiliser HTTPS en production
- Domaine avec certificat SSL

### Trouver l'IP Locale

**Windows** :
```powershell
ipconfig
# Chercher "Adresse IPv4"
```

**macOS/Linux** :
```bash
ifconfig
# Ou
ip addr show
```

## 🚀 Configurations Avancées

### Timeout Personnalisé

```java
OkHttpClient client = new OkHttpClient.Builder()
    .connectTimeout(30, TimeUnit.SECONDS)
    .readTimeout(30, TimeUnit.SECONDS)
    .writeTimeout(30, TimeUnit.SECONDS)
    .build();

Retrofit.Builder builder = new Retrofit.Builder()
    .baseUrl(BASE_URL)
    .client(client);
```

### Logging des Requêtes

```java
HttpLoggingInterceptor logging = new HttpLoggingInterceptor();
logging.setLevel(HttpLoggingInterceptor.Level.BODY);

OkHttpClient client = new OkHttpClient.Builder()
    .addInterceptor(logging)
    .build();

Retrofit.Builder builder = new Retrofit.Builder()
    .baseUrl(BASE_URL)
    .client(client);
```

### Headers Automatiques

```java
OkHttpClient client = new OkHttpClient.Builder()
    .addInterceptor(chain -> {
        Request original = chain.request();
        Request request = original.newBuilder()
            .header("User-Agent", "Android-App")
            .header("Accept-Language", "fr-FR")
            .method(original.method(), original.body())
            .build();
        return chain.proceed(request);
    })
    .build();
```

### Gestion du Cache

```java
int cacheSize = 10 * 1024 * 1024; // 10 MB
Cache cache = new Cache(context.getCacheDir(), cacheSize);

OkHttpClient client = new OkHttpClient.Builder()
    .cache(cache)
    .build();
```

### Authentification

```java
OkHttpClient client = new OkHttpClient.Builder()
    .addInterceptor(chain -> {
        Request request = chain.request().newBuilder()
            .addHeader("Authorization", "Bearer " + getToken())
            .build();
        return chain.proceed(request);
    })
    .build();
```

## 🔒 Sécurité

### ⚠️ Problèmes Actuels

1. **HTTP non chiffré** : Utilise HTTP au lieu de HTTPS
2. **Pas d'authentification** : Aucune sécurité sur les endpoints
3. **Pas de validation SSL** : Pas de certificat

### ✅ Solutions pour Production

**1. Utiliser HTTPS** :
```java
private static final String BASE_URL = "https://api.monapp.com/";
```

**2. Ajouter un Token** :
```java
.addInterceptor(chain -> {
    Request request = chain.request().newBuilder()
        .addHeader("Authorization", "Bearer " + token)
        .build();
    return chain.proceed(request);
})
```

**3. Validation SSL** :
```java
// Configuration du TrustManager pour certificats personnalisés
```

## 🧪 Tests

### Test de Création d'Instance

```java
@Test
public void testGetClientJSON() {
    Retrofit retrofit = RetrofitClient.getClient("JSON");
    assertNotNull(retrofit);
    assertTrue(retrofit.baseUrl().toString().contains("8082"));
}
```

### Test de Singleton

```java
@Test
public void testSingletonPattern() {
    Retrofit instance1 = RetrofitClient.getClient("JSON");
    Retrofit instance2 = RetrofitClient.getClient("JSON");
    assertSame(instance1, instance2);
}
```

### Test de Changement de Format

```java
@Test
public void testFormatChange() {
    Retrofit retrofitJSON = RetrofitClient.getClient("JSON");
    Retrofit retrofitXML = RetrofitClient.getClient("XML");
    assertNotSame(retrofitJSON, retrofitXML);
}
```

## 💡 Best Practices

### ✅ Implémentées

1. **Pattern Singleton** : Une seule instance
2. **Configuration centralisée** : Un seul endroit pour l'URL
3. **Convertisseurs dynamiques** : Support JSON et XML
4. **Constantes** : BASE_URL en constante

### 🚀 Améliorations Possibles

1. **BuildConfig** : Utiliser BuildConfig pour les URLs
```java
private static final String BASE_URL = BuildConfig.API_URL;
```

2. **Injection de dépendances** : Utiliser Dagger/Hilt
```java
@Provides
@Singleton
Retrofit provideRetrofit() {
    return new Retrofit.Builder()...
}
```

3. **Configuration par environnement** :
```java
public enum Environment {
    DEV("http://10.0.2.2:8082/"),
    PROD("https://api.prod.com/");
    
    private String url;
}
```

4. **Gestion d'erreurs globale** :
```java
.addInterceptor(new ErrorInterceptor())
```

## 📚 Dépendances

```gradle
// Retrofit
implementation "com.squareup.retrofit2:retrofit:2.9.0"

// Convertisseurs
implementation "com.squareup.retrofit2:converter-gson:2.9.0"
implementation "com.squareup.retrofit2:converter-simplexml:2.9.0"

// SimpleXML
implementation "org.simpleframework:simple-xml:2.7.1"

// OkHttp (optionnel pour configuration avancée)
implementation "com.squareup.okhttp3:logging-interceptor:4.9.0"
```

## 🔍 Débogage

### Afficher l'URL Utilisée

```java
Retrofit retrofit = RetrofitClient.getClient("JSON");
Log.d("Retrofit", "Base URL: " + retrofit.baseUrl());
```

### Tester la Connexion

```java
// Essayer un appel simple
CompteService service = retrofit.create(CompteService.class);
Call<List<Compte>> call = service.getAllCompteJson();
call.enqueue(new Callback<List<Compte>>() {
    @Override
    public void onResponse(Call<List<Compte>> call, Response<List<Compte>> response) {
        Log.d("Retrofit", "Connexion OK: " + response.code());
    }
    
    @Override
    public void onFailure(Call<List<Compte>> call, Throwable t) {
        Log.e("Retrofit", "Erreur: " + t.getMessage());
    }
});
```

## 📚 Ressources

- [Retrofit Documentation](https://square.github.io/retrofit/)
- [OkHttp Documentation](https://square.github.io/okhttp/)
- [Gson Documentation](https://github.com/google/gson)

---

**Package** : `ma.projet.restclient.config`  
**Version** : 1.0
