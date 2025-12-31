# 🌐 Package API - Services REST

## 📋 Description

Ce package contient les interfaces Retrofit qui définissent les endpoints de l'API REST. Ces interfaces sont utilisées pour :
- Définir les routes HTTP
- Spécifier les paramètres de requête
- Configurer les headers
- Typer les réponses

## 📁 Classes

### CompteService.java
**Rôle** : Interface Retrofit définissant tous les endpoints pour la gestion des comptes

## 🔌 Endpoints Disponibles

### 1. GET - Récupérer tous les comptes (JSON)

```java
@GET("api/comptes")
@Headers("Accept: application/json")
Call<List<Compte>> getAllCompteJson();
```

**Description** : Récupère la liste complète des comptes au format JSON

**Réponse** :
```json
[
  {
    "id": 1,
    "solde": 5000.0,
    "type": "COURANT",
    "dateCreation": "2025-11-26"
  },
  {
    "id": 2,
    "solde": 3000.0,
    "type": "EPARGNE",
    "dateCreation": "2025-11-25"
  }
]
```

### 2. GET - Récupérer tous les comptes (XML)

```java
@GET("api/comptes")
@Headers("Accept: application/xml")
Call<CompteList> getAllCompteXml();
```

**Description** : Récupère la liste complète des comptes au format XML

**Réponse** :
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

### 3. GET - Récupérer un compte par ID

```java
@GET("api/comptes/{id}")
Call<Compte> getCompteById(@Path("id") Long id);
```

**Description** : Récupère un compte spécifique par son identifiant

**Paramètres** :
- `id` (Long) : Identifiant du compte

**Exemple d'utilisation** :
```java
Call<Compte> call = compteService.getCompteById(1L);
```

**Réponse** :
```json
{
  "id": 1,
  "solde": 5000.0,
  "type": "COURANT",
  "dateCreation": "2025-11-26"
}
```

### 4. POST - Créer un nouveau compte

```java
@POST("api/comptes")
Call<Compte> addCompte(@Body Compte compte);
```

**Description** : Crée un nouveau compte bancaire

**Body** :
```json
{
  "solde": 5000.0,
  "type": "COURANT",
  "dateCreation": "2025-11-26"
}
```

**Exemple d'utilisation** :
```java
Compte nouveauCompte = new Compte(null, 5000.0, "COURANT", "2025-11-26");
Call<Compte> call = compteService.addCompte(nouveauCompte);
```

**Réponse** : Le compte créé avec son ID généré
```json
{
  "id": 3,
  "solde": 5000.0,
  "type": "COURANT",
  "dateCreation": "2025-11-26"
}
```

### 5. PUT - Mettre à jour un compte

```java
@PUT("api/comptes/{id}")
Call<Compte> updateCompte(@Path("id") Long id, @Body Compte compte);
```

**Description** : Met à jour un compte existant

**Paramètres** :
- `id` (Long) : Identifiant du compte à modifier
- `compte` (Compte) : Nouvelles données du compte

**Exemple d'utilisation** :
```java
Compte compteModifie = new Compte(1L, 6000.0, "EPARGNE", "2025-11-26");
Call<Compte> call = compteService.updateCompte(1L, compteModifie);
```

**Body** :
```json
{
  "id": 1,
  "solde": 6000.0,
  "type": "EPARGNE",
  "dateCreation": "2025-11-26"
}
```

### 6. DELETE - Supprimer un compte

```java
@DELETE("api/comptes/{id}")
Call<Void> deleteCompte(@Path("id") Long id);
```

**Description** : Supprime un compte par son identifiant

**Paramètres** :
- `id` (Long) : Identifiant du compte à supprimer

**Exemple d'utilisation** :
```java
Call<Void> call = compteService.deleteCompte(1L);
```

**Réponse** : 204 No Content (succès) ou 404 Not Found

## 🔧 Annotations Retrofit

### @GET
Définit une requête HTTP GET
```java
@GET("api/comptes")
```

### @POST
Définit une requête HTTP POST
```java
@POST("api/comptes")
```

### @PUT
Définit une requête HTTP PUT
```java
@PUT("api/comptes/{id}")
```

### @DELETE
Définit une requête HTTP DELETE
```java
@DELETE("api/comptes/{id}")
```

### @Path
Remplace un paramètre dans l'URL
```java
@Path("id") Long id
// api/comptes/{id} → api/comptes/1
```

### @Body
Envoie un objet dans le corps de la requête
```java
@Body Compte compte
```

### @Headers
Ajoute des headers HTTP
```java
@Headers("Accept: application/json")
```

## 🔄 Utilisation Asynchrone

### Avec Callbacks

```java
Call<List<Compte>> call = compteService.getAllCompteJson();
call.enqueue(new Callback<List<Compte>>() {
    @Override
    public void onResponse(Call<List<Compte>> call, Response<List<Compte>> response) {
        if (response.isSuccessful()) {
            List<Compte> comptes = response.body();
            // Traiter les données
        }
    }
    
    @Override
    public void onFailure(Call<List<Compte>> call, Throwable t) {
        // Gérer l'erreur
        Log.e("API", "Erreur: " + t.getMessage());
    }
});
```

### Appel Synchrone (Non recommandé pour Android)

```java
// ⚠️ NE PAS faire sur le thread UI
try {
    Response<List<Compte>> response = compteService.getAllCompteJson().execute();
    if (response.isSuccessful()) {
        List<Compte> comptes = response.body();
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

## 📊 Codes de Réponse HTTP

| Code | Signification | Description |
|------|---------------|-------------|
| 200 | OK | Requête réussie (GET, PUT) |
| 201 | Created | Ressource créée (POST) |
| 204 | No Content | Suppression réussie (DELETE) |
| 400 | Bad Request | Données invalides |
| 404 | Not Found | Ressource introuvable |
| 500 | Server Error | Erreur serveur |

## 🧪 Tests

### Test d'un Endpoint avec Mockito

```java
@Test
public void testGetAllCompteJson() {
    CompteService service = mock(CompteService.class);
    List<Compte> comptes = Arrays.asList(
        new Compte(1L, 5000.0, "COURANT", "2025-11-26")
    );
    
    Call<List<Compte>> call = mock(Call.class);
    when(service.getAllCompteJson()).thenReturn(call);
    
    verify(service).getAllCompteJson();
}
```

### Test avec MockWebServer

```java
@Test
public void testApiResponse() throws IOException {
    MockWebServer server = new MockWebServer();
    server.enqueue(new MockResponse()
        .setBody("[{\"id\":1,\"solde\":5000.0,\"type\":\"COURANT\",\"dateCreation\":\"2025-11-26\"}]")
        .setResponseCode(200));
    
    server.start();
    
    Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(server.url("/"))
        .addConverterFactory(GsonConverterFactory.create())
        .build();
    
    CompteService service = retrofit.create(CompteService.class);
    Response<List<Compte>> response = service.getAllCompteJson().execute();
    
    assertTrue(response.isSuccessful());
    assertEquals(1, response.body().size());
    
    server.shutdown();
}
```

## 💡 Best Practices

### ✅ Implémentées

1. **Headers explicites** : Spécifier JSON ou XML
2. **Types génériques** : Call<List<Compte>> plutôt que Call<Object>
3. **Nommage clair** : Méthodes descriptives
4. **Séparation JSON/XML** : Méthodes distinctes

### 🚀 Améliorations Possibles

1. **Paramètres de requête** :
```java
@GET("api/comptes")
Call<List<Compte>> getComptesByType(@Query("type") String type);
```

2. **Pagination** :
```java
@GET("api/comptes")
Call<List<Compte>> getComptes(
    @Query("page") int page,
    @Query("size") int size
);
```

3. **Headers dynamiques** :
```java
@GET("api/comptes")
Call<List<Compte>> getAllComptes(@Header("Accept") String format);
```

4. **Timeout personnalisé** :
```java
@GET("api/comptes")
@Headers("Connection: close")
Call<List<Compte>> getAllComptes();
```

## 🔒 Sécurité

### Ajouter l'Authentification

**Token Bearer** :
```java
@GET("api/comptes")
Call<List<Compte>> getAllComptes(@Header("Authorization") String token);
```

**Usage** :
```java
String token = "Bearer " + authToken;
Call<List<Compte>> call = compteService.getAllComptes(token);
```

**Intercepteur Retrofit** :
```java
OkHttpClient client = new OkHttpClient.Builder()
    .addInterceptor(chain -> {
        Request request = chain.request().newBuilder()
            .addHeader("Authorization", "Bearer " + token)
            .build();
        return chain.proceed(request);
    })
    .build();
```

## 📚 Ressources

- [Retrofit Documentation](https://square.github.io/retrofit/)
- [HTTP Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [REST API Best Practices](https://restfulapi.net/)

---

**Package** : `ma.projet.restclient.api`  
**Version** : 1.0
