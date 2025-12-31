# 📦 Package Entities - Modèles de Données

## 📋 Description

Ce package contient les classes représentant les entités métier de l'application. Ces classes sont utilisées pour :
- Représenter les données du backend
- Sérialiser/Désérialiser JSON et XML
- Transférer les données entre les couches

## 📁 Classes

### Compte.java
**Rôle** : Modèle principal représentant un compte bancaire

#### Propriétés

| Propriété | Type | Description |
|-----------|------|-------------|
| `id` | Long | Identifiant unique du compte (généré par le backend) |
| `solde` | double | Solde actuel du compte |
| `type` | String | Type de compte : "COURANT" ou "EPARGNE" |
| `dateCreation` | String | Date de création au format ISO (yyyy-MM-dd) |

#### Annotations

**Pour XML (SimpleXML)** :
- `@Root(name = "item", strict = false)` : Définit l'élément racine XML
- `@Element(name = "...")` : Map chaque propriété à un élément XML

**Pour JAXB** :
- `@XmlElement(name = "...")` : Compatibilité JAXB pour les getters

#### Constructeurs

```java
// Constructeur par défaut (requis pour la désérialisation)
public Compte()

// Constructeur avec paramètres
public Compte(Long id, double solde, String type, String dateCreation)
```

#### Exemple d'Utilisation

**Création d'un nouveau compte** :
```java
Compte compte = new Compte(null, 5000.0, "COURANT", "2025-11-26");
```

**Modification d'un compte existant** :
```java
compte.setSolde(6000.0);
compte.setType("EPARGNE");
```

#### Format JSON

```json
{
  "id": 1,
  "solde": 5000.0,
  "type": "COURANT",
  "dateCreation": "2025-11-26"
}
```

#### Format XML

```xml
<item>
  <id>1</id>
  <solde>5000.0</solde>
  <type>COURANT</type>
  <dateCreation>2025-11-26</dateCreation>
</item>
```

### CompteList.java
**Rôle** : Wrapper pour la désérialisation des listes XML

#### Propriétés

| Propriété | Type | Description |
|-----------|------|-------------|
| `comptes` | List<Compte> | Liste des comptes bancaires |

#### Annotations

- `@Root(name = "List", strict = false)` : Élément racine pour la liste
- `@ElementList(inline = true, entry = "item")` : Liste inline avec éléments nommés "item"

#### Utilisation

Cette classe est utilisée automatiquement par Retrofit lors de la désérialisation de réponses XML comme :

```xml
<List>
  <item>
    <id>1</id>
    <solde>5000.0</solde>
    <type>COURANT</type>
    <dateCreation>2025-11-26</dateCreation>
  </item>
  <item>
    <id>2</id>
    <solde>3000.0</solde>
    <type>EPARGNE</type>
    <dateCreation>2025-11-25</dateCreation>
  </item>
</List>
```

**Dans le Repository** :
```java
Call<CompteList> call = compteService.getAllCompteXml();
call.enqueue(new Callback<CompteList>() {
    @Override
    public void onResponse(Call<CompteList> call, Response<CompteList> response) {
        List<Compte> comptes = response.body().getComptes();
        // Utiliser la liste de comptes
    }
});
```

## 🔧 Dépendances

### SimpleXML
```gradle
implementation "org.simpleframework:simple-xml:2.7.1"
```

**Annotations utilisées** :
- `@Root` : Définit une classe comme élément racine XML
- `@Element` : Map une propriété à un élément XML
- `@ElementList` : Map une liste à des éléments XML

### JAXB
```gradle
implementation "com.squareup.retrofit2:converter-jaxb:2.9.0"
```

**Annotations utilisées** :
- `@XmlElement` : Map les getters aux éléments XML

## 📝 Best Practices

### ✅ Implémentées

1. **Constructeur par défaut** : Requis pour la désérialisation
2. **Getters/Setters** : Accès contrôlé aux propriétés
3. **toString()** : Facilite le débogage
4. **Annotations doubles** : Support JSON et XML

### 💡 Recommandations

1. **Validation** : Ajouter des validations dans les setters
   ```java
   public void setSolde(double solde) {
       if (solde < 0) {
           throw new IllegalArgumentException("Le solde ne peut pas être négatif");
       }
       this.solde = solde;
   }
   ```

2. **Enum pour le type** : Utiliser un enum plutôt qu'une String
   ```java
   public enum TypeCompte {
       COURANT, EPARGNE
   }
   ```

3. **LocalDate** : Utiliser LocalDate au lieu de String pour les dates
   ```java
   private LocalDate dateCreation;
   ```

4. **Equals & HashCode** : Implémenter pour comparer les comptes
   ```java
   @Override
   public boolean equals(Object o) {
       if (this == o) return true;
       if (o == null || getClass() != o.getClass()) return false;
       Compte compte = (Compte) o;
       return Objects.equals(id, compte.id);
   }
   ```

## 🧪 Tests Possibles

### Test de Sérialisation JSON
```java
@Test
public void testSerializationJSON() {
    Compte compte = new Compte(1L, 5000.0, "COURANT", "2025-11-26");
    Gson gson = new Gson();
    String json = gson.toJson(compte);
    assertNotNull(json);
    assertTrue(json.contains("\"solde\":5000.0"));
}
```

### Test de Désérialisation JSON
```java
@Test
public void testDeserializationJSON() {
    String json = "{\"id\":1,\"solde\":5000.0,\"type\":\"COURANT\",\"dateCreation\":\"2025-11-26\"}";
    Gson gson = new Gson();
    Compte compte = gson.fromJson(json, Compte.class);
    assertEquals(Long.valueOf(1), compte.getId());
    assertEquals(5000.0, compte.getSolde(), 0.01);
}
```

## 🔍 Débogage

### Afficher un compte
```java
Compte compte = ...;
Log.d("Compte", compte.toString());
// Output: Compte{id=1, solde=5000.0, type='COURANT', dateCreation='2025-11-26'}
```

### Vérifier la désérialisation XML
```java
Serializer serializer = new Persister();
CompteList compteList = serializer.read(CompteList.class, xmlString);
for (Compte compte : compteList.getComptes()) {
    Log.d("XML", compte.toString());
}
```

## 📚 Ressources

- [SimpleXML Documentation](http://simple.sourceforge.net/)
- [JAXB Tutorial](https://docs.oracle.com/javase/tutorial/jaxb/)
- [Gson User Guide](https://github.com/google/gson/blob/master/UserGuide.md)

---

**Package** : `ma.projet.restclient.entities`  
**Version** : 1.0
