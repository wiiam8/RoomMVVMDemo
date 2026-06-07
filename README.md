# LAB 19 - Room, MVVM, Repository, ViewModel, LiveData et RecyclerView

## Description

Ce projet est une application Android développée en Java dans le cadre du cours **Programmation Mobile : Android avec Java**.

Le laboratoire porte sur la création d’une application de gestion de notes en utilisant une architecture moderne basée sur :

* Room
* MVVM
* Repository
* ViewModel
* LiveData
* RecyclerView

L’objectif principal est de comprendre comment structurer proprement une application Android afin de séparer l’interface utilisateur, la logique de présentation et l’accès aux données locales.

L’application réalisée s’appelle **RoomMVVMDemo**. Elle permet d’ajouter des notes composées d’un titre et d’une description, de les afficher dans une liste, de les supprimer par clic long, de supprimer toutes les notes, et de conserver les données localement grâce à Room.

## Objectifs du laboratoire

Ce lab permet de mettre en pratique les notions suivantes :

* Comprendre l’architecture MVVM
* Comprendre le rôle d’une Entity Room
* Créer une base locale avec Room
* Créer un DAO pour accéder aux données
* Créer une classe RoomDatabase
* Créer un Repository
* Créer un ViewModel
* Utiliser LiveData pour observer automatiquement les données
* Afficher une liste avec RecyclerView
* Créer un Adapter RecyclerView
* Créer un layout pour chaque élément de la liste
* Insérer des données dans une base Room
* Supprimer une note par clic long
* Supprimer toutes les notes
* Persister les données localement
* Éviter les opérations bloquantes sur le thread principal
* Comprendre la différence entre état d’interface et persistance réelle

## Fonctionnalités réalisées

L’application permet de :

* Saisir un titre de note
* Saisir une description de note
* Ajouter une note dans la base locale
* Afficher toutes les notes existantes dans un RecyclerView
* Trier les notes de la plus récente à la plus ancienne
* Afficher chaque note dans une CardView
* Supprimer une note avec un clic long
* Supprimer toutes les notes avec un bouton
* Afficher des messages de confirmation avec Toast
* Observer automatiquement les changements dans la base de données
* Conserver les notes après fermeture et réouverture de l’application
* Garder un affichage cohérent après rotation de l’écran

## Technologies utilisées

* Java
* Android Studio
* Android SDK
* XML
* AppCompat
* Material Components
* Room
* SQLite
* RoomDatabase
* DAO
* Repository
* AndroidViewModel
* LiveData
* RecyclerView
* CardView
* ExecutorService

## Structure du projet

```
RoomMVVMDemo/
│
├── settings.gradle.kts
├── build.gradle.kts
│
├── app/
│   ├── build.gradle.kts
│   │
│   └── src/
│       └── main/
│           ├── AndroidManifest.xml
│           │
│           ├── java/
│           │   └── com/
│           │       └── example/
│           │           └── roommvvmdemo/
│           │               │
│           │               ├── data/
│           │               │   │
│           │               │   ├── local/
│           │               │   │   ├── Note.java
│           │               │   │   ├── NoteDao.java
│           │               │   │   └── NoteDatabase.java
│           │               │   │
│           │               │   └── NoteRepository.java
│           │               │
│           │               ├── ui/
│           │               │   ├── MainActivity.java
│           │               │   └── NoteAdapter.java
│           │               │
│           │               └── viewmodel/
│           │                   └── NoteViewModel.java
│           │
│           └── res/
│               ├── layout/
│               │   ├── activity_main.xml
│               │   └── note_item.xml
│               │
│               └── values/
│                   ├── strings.xml
│                   └── themes.xml
│
├── README.md
└── .gitignore
```

## Package utilisé

Le package principal du projet est :

```
com.example.roommvvmdemo
```

Les packages internes sont :

```
com.example.roommvvmdemo.data
com.example.roommvvmdemo.data.local
com.example.roommvvmdemo.ui
com.example.roommvvmdemo.viewmodel
```

## Architecture globale

L’application suit une architecture MVVM.

Le flux général est le suivant :

```
Utilisateur
    |
    | saisit une note ou clique sur un bouton
    v
MainActivity
    |
    | appelle le ViewModel
    v
NoteViewModel
    |
    | délègue au Repository
    v
NoteRepository
    |
    | utilise le DAO
    v
NoteDao
    |
    | communique avec Room
    v
NoteDatabase / SQLite
    |
    | renvoie LiveData<List<Note>>
    v
NoteViewModel
    |
    | expose les données observables
    v
MainActivity
    |
    | observe les données et met à jour l’Adapter
    v
RecyclerView
```

Cette organisation permet de séparer les responsabilités et de garder un code plus propre, plus maintenable et plus proche des pratiques professionnelles Android.

## Rôle de chaque couche

### Entity

L’Entity représente la structure d’une table dans la base de données.

Dans ce projet, l’Entity est :

```
Note.java
```

Elle représente la table :

```
notes_table
```

Chaque note contient :

* un identifiant généré automatiquement ;
* un titre ;
* une description.

### DAO

Le DAO est l’interface qui définit les opérations possibles sur la base.

Dans ce projet, le DAO est :

```
NoteDao.java
```

Il contient les opérations suivantes :

* insérer une note ;
* supprimer une note ;
* supprimer toutes les notes ;
* récupérer toutes les notes sous forme de LiveData.

### RoomDatabase

La classe RoomDatabase représente le point central de la base locale.

Dans ce projet, la base est :

```
NoteDatabase.java
```

Elle crée et fournit l’accès au DAO.

Elle utilise le pattern Singleton afin d’éviter la création de plusieurs instances de base de données.

### Repository

Le Repository est une couche intermédiaire entre le ViewModel et les données.

Dans ce projet, le Repository est :

```
NoteRepository.java
```

Son rôle est de :

* cacher les détails d’accès à Room ;
* centraliser les opérations de données ;
* exécuter les insertions et suppressions en arrière-plan ;
* exposer la liste observable des notes.

### ViewModel

Le ViewModel contient la logique liée à l’écran.

Dans ce projet, le ViewModel est :

```
NoteViewModel.java
```

Son rôle est de :

* recevoir les actions venant de l’interface ;
* appeler le Repository ;
* exposer les notes à l’Activity ;
* survivre aux changements de configuration comme la rotation d’écran.

### LiveData

LiveData permet d’observer automatiquement les données.

Dans ce projet, le DAO retourne :

```
LiveData<List<Note>>
```

Ainsi, lorsque la base change, Room met automatiquement à jour le LiveData.

L’Activity observe cette donnée et met à jour le RecyclerView.

### RecyclerView

RecyclerView permet d’afficher une liste de notes de manière performante.

Dans ce projet, il est utilisé avec :

```
NoteAdapter.java
note_item.xml
```

Chaque note est affichée sous forme de carte contenant :

* un titre ;
* une description.

## Description des fichiers principaux

### Note.java

Chemin :

```
app/src/main/java/com/example/roommvvmdemo/data/local/Note.java
```

Cette classe représente une note persistée dans la base Room.

Elle contient :

```
id
title
description
```

L’annotation utilisée est :

```
@Entity(tableName = "notes_table")
```

La clé primaire est :

```
@PrimaryKey(autoGenerate = true)
```

Room génère automatiquement l’identifiant de chaque note lors de l’insertion.

### NoteDao.java

Chemin :

```
app/src/main/java/com/example/roommvvmdemo/data/local/NoteDao.java
```

Cette interface définit les requêtes d’accès aux notes.

Elle contient :

```
insert(Note note)
delete(Note note)
deleteAllNotes()
getAllNotes()
```

La méthode `getAllNotes()` retourne :

```
LiveData<List<Note>>
```

Ce choix permet à l’interface d’être mise à jour automatiquement quand les données changent.

### NoteDatabase.java

Chemin :

```
app/src/main/java/com/example/roommvvmdemo/data/local/NoteDatabase.java
```

Cette classe représente la base Room.

Elle contient :

* l’annotation `@Database`
* une méthode abstraite `noteDao()`
* une instance Singleton
* une méthode `getInstance(Context context)`

La base est nommée :

```
notes_database
```

La table principale est :

```
notes_table
```

### NoteRepository.java

Chemin :

```
app/src/main/java/com/example/roommvvmdemo/data/NoteRepository.java
```

Cette classe sert d’intermédiaire entre le ViewModel et Room.

Elle contient :

* une référence vers `NoteDao`
* une référence vers `LiveData<List<Note>>`
* un `ExecutorService`

Les méthodes d’écriture utilisent un thread secondaire :

```
executorService.execute(...)
```

Cela évite d’exécuter Room sur le thread principal.

### NoteViewModel.java

Chemin :

```
app/src/main/java/com/example/roommvvmdemo/viewmodel/NoteViewModel.java
```

Cette classe hérite de :

```
AndroidViewModel
```

Elle contient :

* une instance de `NoteRepository`
* une liste observable de notes
* des méthodes pour insérer, supprimer et supprimer toutes les notes

Elle expose les données à l’interface via :

```
LiveData<List<Note>>
```

### NoteAdapter.java

Chemin :

```
app/src/main/java/com/example/roommvvmdemo/ui/NoteAdapter.java
```

Cette classe permet au RecyclerView d’afficher les notes.

Elle contient :

* une liste de notes ;
* un `ViewHolder` ;
* une méthode `setNotes()` ;
* un clic simple ;
* un clic long.

Le clic long est utilisé pour supprimer une note.

### MainActivity.java

Chemin :

```
app/src/main/java/com/example/roommvvmdemo/ui/MainActivity.java
```

Cette classe représente l’interface principale.

Elle contient :

* deux champs de saisie ;
* un bouton d’ajout ;
* un bouton de suppression globale ;
* un RecyclerView ;
* un NoteAdapter ;
* un NoteViewModel.

Elle observe les notes avec :

```
noteViewModel.getAllNotes().observe(...)
```

Quand la liste change, l’Adapter est mis à jour automatiquement.

## Interface utilisateur

L’interface principale se trouve dans :

```
app/src/main/res/layout/activity_main.xml
```

Elle contient :

* un titre ;
* un champ pour le titre de la note ;
* un champ pour la description ;
* un bouton pour ajouter une note ;
* un bouton pour supprimer toutes les notes ;
* un texte d’information ;
* un RecyclerView.

Les identifiants principaux sont :

```
tvHeader
etTitle
etDescription
btnAdd
btnDeleteAll
tvInfo
recyclerView
```

## Layout d’une note

Le layout d’une note se trouve dans :

```
app/src/main/res/layout/note_item.xml
```

Il utilise :

```
CardView
```

Chaque carte contient :

* `tvTitle`
* `tvDescription`

Ce layout donne un affichage plus clair et plus professionnel à la liste des notes.

## Dépendances utilisées

Les dépendances principales du projet sont :

```
implementation("androidx.appcompat:appcompat:1.7.0")
implementation("com.google.android.material:material:1.12.0")
implementation("androidx.constraintlayout:constraintlayout:2.2.0")

implementation("androidx.room:room-runtime:2.8.4")
annotationProcessor("androidx.room:room-compiler:2.8.4")
implementation("androidx.room:room-livedata:2.8.4")

implementation("androidx.lifecycle:lifecycle-viewmodel:2.9.3")
implementation("androidx.lifecycle:lifecycle-livedata:2.9.3")

implementation("androidx.recyclerview:recyclerview:1.4.0")
implementation("androidx.cardview:cardview:1.0.0")
```

## Explication des dépendances

### Room Runtime

```
androidx.room:room-runtime
```

Fournit les fonctionnalités principales de Room.

### Room Compiler

```
androidx.room:room-compiler
```

Génère automatiquement le code nécessaire à partir des annotations Room.

### Room LiveData

```
androidx.room:room-livedata
```

Permet à Room de retourner des résultats observables via LiveData.

### Lifecycle ViewModel

```
androidx.lifecycle:lifecycle-viewmodel
```

Fournit les classes ViewModel et AndroidViewModel.

### Lifecycle LiveData

```
androidx.lifecycle:lifecycle-livedata
```

Fournit LiveData et les mécanismes d’observation.

### RecyclerView

```
androidx.recyclerview:recyclerview
```

Permet d’afficher des listes performantes.

### CardView

```
androidx.cardview:cardview
```

Permet d’afficher chaque note sous forme de carte.

## Fonctionnement de l’ajout d’une note

Quand l’utilisateur ajoute une note :

1. L’utilisateur saisit un titre.
2. L’utilisateur saisit une description.
3. Il clique sur le bouton `AJOUTER UNE NOTE`.
4. MainActivity vérifie que les champs ne sont pas vides.
5. MainActivity crée un objet `Note`.
6. MainActivity appelle `noteViewModel.insert(note)`.
7. Le ViewModel appelle le Repository.
8. Le Repository insère la note dans Room via le DAO.
9. L’insertion est exécutée dans un thread secondaire.
10. Room met à jour la base SQLite.
11. Le LiveData est automatiquement notifié.
12. MainActivity reçoit la nouvelle liste.
13. L’Adapter met à jour le RecyclerView.

## Fonctionnement de la suppression d’une note

La suppression se fait par clic long.

Quand l’utilisateur effectue un clic long sur une note :

1. Le `NoteAdapter` détecte le clic long.
2. Il renvoie la note sélectionnée à MainActivity.
3. MainActivity appelle `noteViewModel.delete(note)`.
4. Le ViewModel appelle le Repository.
5. Le Repository appelle le DAO en arrière-plan.
6. Room supprime la note dans SQLite.
7. LiveData notifie la nouvelle liste.
8. RecyclerView se met à jour automatiquement.

## Fonctionnement de la suppression globale

Quand l’utilisateur clique sur :

```
SUPPRIMER TOUTES LES NOTES
```

Le flux est :

```
MainActivity
    -> NoteViewModel
    -> NoteRepository
    -> NoteDao
    -> Room / SQLite
```

La requête exécutée est :

```
DELETE FROM notes_table
```

La liste devient ensuite vide et le RecyclerView est mis à jour automatiquement.

## Pourquoi utiliser Room

Room est utilisé car il fournit une abstraction propre au-dessus de SQLite.

Ses avantages sont :

* code plus lisible ;
* requêtes structurées ;
* vérification des requêtes à la compilation ;
* intégration avec LiveData ;
* réduction du code répétitif ;
* meilleure organisation des données ;
* architecture plus professionnelle.

## Pourquoi utiliser MVVM

MVVM permet de séparer les responsabilités.

Sans MVVM, l’Activity risque de contenir :

* l’interface ;
* la logique ;
* les requêtes SQL ;
* la gestion des threads ;
* la mise à jour de la liste.

Avec MVVM :

* MainActivity gère l’interface ;
* ViewModel gère la logique de présentation ;
* Repository gère l’accès aux données ;
* Room gère la persistance ;
* LiveData gère l’observation.

Cette séparation rend le projet plus propre et plus facile à maintenir.

## Pourquoi utiliser Repository

Le Repository évite que le ViewModel parle directement au DAO.

C’est une couche stratégique.

Il permet :

* de cacher la source réelle des données ;
* d’isoler Room ;
* de préparer une future API distante ;
* de centraliser les règles d’accès aux données ;
* d’exécuter les opérations en arrière-plan.

Dans une application plus avancée, le Repository pourrait combiner :

* Room ;
* Retrofit ;
* cache local ;
* API distante ;
* logique de synchronisation.

## Pourquoi utiliser ViewModel

Le ViewModel conserve les données utiles à l’écran pendant les changements de configuration.

Il aide notamment lors de :

* rotation d’écran ;
* changement de thème ;
* recréation temporaire de l’Activity.

Dans ce projet, le ViewModel expose :

```
LiveData<List<Note>>
```

Ainsi, l’Activity peut être recréée sans casser l’observation des notes.

## Limite du ViewModel

Le ViewModel ne remplace pas une vraie persistance.

Il survit aux changements de configuration, mais pas forcément à une vraie mort du processus.

Dans ce projet, les notes restent quand même disponibles après fermeture complète de l’application, car elles sont stockées dans Room.

Il faut donc distinguer :

```
ViewModel = état d’interface
Room = persistance locale
```

## Pourquoi utiliser LiveData

LiveData permet à l’interface d’observer les données sans relire manuellement la base.

Dans ce projet :

```
NoteDao retourne LiveData<List<Note>>
NoteRepository transmet ce LiveData
NoteViewModel expose ce LiveData
MainActivity observe ce LiveData
RecyclerView est mis à jour automatiquement
```

LiveData respecte aussi le cycle de vie de l’Activity.

Cela évite les mises à jour inutiles quand l’écran n’est plus actif.

## Pourquoi utiliser RecyclerView

RecyclerView est adapté à l’affichage de listes.

Il est plus performant qu’une simple succession de TextView, car il recycle les vues hors écran.

Dans ce projet, RecyclerView affiche toutes les notes.

Chaque élément est contrôlé par :

```
NoteAdapter
NoteHolder
note_item.xml
```

## Pourquoi ne pas exécuter Room sur le thread principal

Les opérations de base de données peuvent prendre du temps.

Si elles sont exécutées sur le thread principal, l’application peut :

* se figer ;
* ralentir ;
* provoquer une mauvaise expérience utilisateur ;
* déclencher des erreurs liées au main thread.

Pour éviter cela, le Repository utilise :

```
ExecutorService
```

Les méthodes concernées sont :

```
insert()
delete()
deleteAllNotes()
```

## Installation et exécution

1. Cloner le dépôt GitHub :

   ```
    git clone https://github.com/wiiam8/RoomMVVMDemo.git
   ```

2. Ouvrir le projet avec Android Studio.

3. Vérifier que le package principal est :

   ```
    com.example.roommvvmdemo
   ```

4. Synchroniser Gradle :

   ```
    Sync Project with Gradle Files
   ```

5. Compiler le projet :

   ```
    Build > Make Project
   ```

6. Lancer l’application sur un émulateur ou un téléphone Android.

7. Saisir un titre de note.

8. Saisir une description.

9. Cliquer sur :

   ```
    AJOUTER UNE NOTE
   ```

10. Vérifier que la note apparaît dans la liste.

11. Faire un clic long sur une note pour la supprimer.

12. Fermer et rouvrir l’application pour vérifier la persistance.

## Résultat attendu

Au lancement, l’application affiche :

* un champ titre ;
* un champ description ;
* un bouton d’ajout ;
* un bouton de suppression globale ;
* un RecyclerView vide ou contenant les notes déjà enregistrées.

Après ajout d’une note, celle-ci apparaît immédiatement dans la liste.

Après fermeture et réouverture de l’application, les notes sont toujours présentes.

Après clic long sur une note, elle disparaît.

Après clic sur suppression globale, toute la liste devient vide.

## Tests de validation

### Test 1 : insertion simple

Action :

```
saisir un titre et une description, puis cliquer sur AJOUTER UNE NOTE.
```

Résultat attendu :

```
la note apparaît dans le RecyclerView.
```

### Test 2 : insertion multiple

Action :

```
ajouter au moins trois notes.
```

Résultat attendu :

```
les trois notes s’affichent dans la liste.
```

### Test 3 : tri par ordre récent

Action :

```
ajouter plusieurs notes successivement.
```

Résultat attendu :

```
la note la plus récente apparaît en haut.
```

### Test 4 : suppression par clic long

Action :

```
effectuer un clic long sur une note.
```

Résultat attendu :

```
la note est supprimée.
```

### Test 5 : suppression globale

Action :

```
cliquer sur SUPPRIMER TOUTES LES NOTES.
```

Résultat attendu :

```
la liste devient vide.
```

### Test 6 : rotation d’écran

Action :

```
ajouter des notes puis tourner l’écran.
```

Résultat attendu :

```
les notes restent visibles.
```

### Test 7 : persistance locale

Action :

```
fermer complètement l’application puis la rouvrir.
```

Résultat attendu :

```
les notes sont toujours présentes.
```

### Test 8 : validation des champs

Action :

```
cliquer sur AJOUTER UNE NOTE avec un champ vide.
```

Résultat attendu :

```
un Toast demande de remplir le titre et la description.
```

### Test 9 : Toast de clic simple

Action :

```
cliquer simplement sur une note.
```

Résultat attendu :

```
un Toast affiche le titre de la note.
```

### Test 10 : données après suppression globale

Action :

```
supprimer toutes les notes puis redémarrer l’application.
```

Résultat attendu :

```
aucune note n’est affichée.
```

## Commandes ADB utiles

Lister les appareils connectés :

```
adb devices
```

Nettoyer les données de l’application :

```
adb shell pm clear com.example.roommvvmdemo
```

Tuer le processus de l’application :

```
adb shell am kill com.example.roommvvmdemo
```

Afficher Logcat :

```
adb logcat
```

Redémarrer l’application après nettoyage :

```
adb shell monkey -p com.example.roommvvmdemo 1
```



## Dépôt GitHub

Lien du dépôt :

```
https://github.com/wiiam8/RoomMVVMDemo
```

## Erreurs fréquentes et solutions

### Erreur : Cannot resolve symbol Room

Cause possible :

```
les dépendances Room sont absentes.
```

Solution :

Ajouter dans `app/build.gradle.kts` :

```
implementation("androidx.room:room-runtime:2.8.4")
annotationProcessor("androidx.room:room-compiler:2.8.4")
implementation("androidx.room:room-livedata:2.8.4")
```

Puis synchroniser Gradle.

### Erreur : Cannot resolve symbol Entity

Cause possible :

```
Room Runtime n’est pas ajouté ou Gradle n’est pas synchronisé.
```

Solution :

Vérifier :

```
implementation("androidx.room:room-runtime:2.8.4")
```

Puis faire :

```
Sync Project with Gradle Files
```

### Erreur : Cannot resolve symbol RecyclerView

Cause possible :

```
dépendance RecyclerView absente.
```

Solution :

Ajouter :

```
implementation("androidx.recyclerview:recyclerview:1.4.0")
```

### Erreur : Cannot resolve symbol CardView

Cause possible :

```
dépendance CardView absente.
```

Solution :

Ajouter :

```
implementation("androidx.cardview:cardview:1.0.0")
```

### Erreur : Cannot resolve symbol ViewModelProvider

Cause possible :

```
dépendance lifecycle-viewmodel absente.
```

Solution :

Ajouter :

```
implementation("androidx.lifecycle:lifecycle-viewmodel:2.9.3")
```

### Erreur : Cannot resolve symbol LiveData

Cause possible :

```
dépendance lifecycle-livedata absente.
```

Solution :

Ajouter :

```
implementation("androidx.lifecycle:lifecycle-livedata:2.9.3")
```

### Erreur : Cannot resolve symbol R

Causes possibles :

* erreur dans un fichier XML ;
* mauvais nom de package ;
* ressource manquante ;
* Gradle non synchronisé ;
* erreur dans strings.xml ;
* erreur dans note_item.xml.

Solutions :

* vérifier `activity_main.xml` ;
* vérifier `note_item.xml` ;
* vérifier `strings.xml` ;
* faire `Build > Clean Project` ;
* faire `Build > Rebuild Project`.

### Erreur : Room cannot verify the data integrity

Cause possible :

```
changement du schéma de base sans migration correcte.
```

Solution en développement :

```
désinstaller l’application ou effacer ses données.
```

Commande utile :

```
adb shell pm clear com.example.roommvvmdemo
```

### Erreur : Cannot access database on the main thread

Cause possible :

```
insertion ou suppression exécutée directement sur le thread principal.
```

Solution :

Utiliser `ExecutorService` dans le Repository :

```
executorService.execute(...)
```

### Erreur : la liste ne se met pas à jour

Causes possibles :

* l’Activity n’observe pas le LiveData ;
* l’Adapter n’appelle pas `setNotes()` ;
* la requête DAO ne retourne pas LiveData ;
* le RecyclerView n’a pas d’Adapter.

Solutions :

Vérifier dans MainActivity :

```
noteViewModel.getAllNotes().observe(...)
```

Vérifier dans NoteAdapter :

```
setNotes(List<Note> notes)
```

Vérifier dans NoteDao :

```
LiveData<List<Note>> getAllNotes();
```

### Erreur : clic long ne supprime pas

Causes possibles :

* `setOnLongClickListener` absent ;
* listener non connecté dans MainActivity ;
* `return true` manquant dans le long click.

Solution :

Vérifier que l’Adapter contient :

```
itemView.setOnLongClickListener(...)
```

Et que MainActivity contient :

```
adapter.setOnItemLongClickListener(...)
```

### Erreur : les notes disparaissent après redémarrage

Causes possibles :

* base effacée manuellement ;
* application désinstallée ;
* `pm clear` exécuté ;
* mauvaise base utilisée ;
* code qui appelle `deleteAllNotes()` au lancement.

Solution :

Vérifier que la base est bien créée avec :

```
"notes_database"
```

Vérifier qu’aucune suppression automatique n’est appelée dans `onCreate()`.

## Bonnes pratiques appliquées

Ce projet applique plusieurs bonnes pratiques Android :

* séparation claire des responsabilités ;
* architecture MVVM ;
* accès aux données via Repository ;
* base locale gérée avec Room ;
* observation des données avec LiveData ;
* affichage performant avec RecyclerView ;
* opérations Room exécutées hors du thread principal ;
* utilisation d’un Singleton pour la base ;
* utilisation de chaînes dans `strings.xml` ;
* validation minimale des champs ;
* suppression par clic long ;
* persistance locale réelle.

## Limites du projet

Ce projet reste un laboratoire pédagogique.

Ses limites principales sont :

* pas de modification d’une note existante ;
* pas de recherche ;
* pas de tri avancé ;
* pas de priorité ;
* pas de date de création ;
* pas de migration Room de production ;
* pas de tests unitaires ;
* pas de pagination ;
* pas d’injection de dépendances ;
* pas de DataStore ;
* pas de synchronisation avec une API distante.

## Améliorations possibles

Pour aller plus loin, il est possible d’ajouter :

* modification d’une note ;
* recherche par titre ;
* tri par date ;
* champ priorité ;
* date de création ;
* date de modification ;
* écran de détail ;
* confirmation avant suppression ;
* DiffUtil dans l’Adapter ;
* ListAdapter ;
* migrations Room propres ;
* tests unitaires du Repository ;
* tests UI ;
* DataBinding ;
* ViewBinding ;
* Navigation Component ;
* Hilt pour injection de dépendances ;
* Retrofit pour synchronisation distante ;
* Room avec Flow ;
* version Kotlin.

## Comparaison entre application classique et application MVVM

| Critère              | Application directe dans Activity | Application MVVM                  |
| -------------------- | --------------------------------- | --------------------------------- |
| Accès à la base      | Dans l’Activity                   | Dans Repository / DAO             |
| Logique métier       | Mélangée avec l’UI                | Dans ViewModel / Repository       |
| Lisibilité           | Faible quand le projet grandit    | Meilleure                         |
| Maintenance          | Difficile                         | Plus simple                       |
| Testabilité          | Faible                            | Meilleure                         |
| Rotation écran       | Risque de recréation confuse      | ViewModel aide à conserver l’état |
| Mise à jour de liste | Manuelle                          | Automatique avec LiveData         |
| Thread principal     | Risque d’opérations bloquantes    | Repository gère l’arrière-plan    |

## Comparaison entre SQLite direct et Room

| Critère              | SQLite direct | Room             |
| -------------------- | ------------- | ---------------- |
| Code nécessaire      | Plus long     | Plus court       |
| Requêtes vérifiées   | À l’exécution | À la compilation |
| Mapping objet/table  | Manuel        | Automatique      |
| LiveData             | Manuel        | Intégré          |
| Lisibilité           | Moyenne       | Meilleure        |
| Maintenance          | Plus lourde   | Plus propre      |
| Architecture moderne | Moins adaptée | Recommandée      |

## Comparaison entre ViewModel et Room

| Élément   | Rôle                                                 |
| --------- | ---------------------------------------------------- |
| ViewModel | Conserve l’état d’écran et expose les données à l’UI |
| Room      | Persiste réellement les données dans SQLite          |

Il ne faut pas confondre les deux.

ViewModel aide pendant une rotation.

Room garde les données après fermeture de l’application.

## Explication pédagogique complète

Dans une application Android non structurée, l’Activity contient souvent trop de responsabilités.

Elle peut gérer :

* les boutons ;
* les champs ;
* la liste ;
* les requêtes SQL ;
* les threads ;
* la logique ;
* les erreurs ;
* la persistance.

Ce type de code fonctionne au début, mais devient vite difficile à comprendre et à maintenir.

L’architecture MVVM règle ce problème en divisant le projet en couches claires.

Dans ce lab :

* `MainActivity` affiche l’interface ;
* `NoteViewModel` reçoit les actions de l’interface ;
* `NoteRepository` gère la source de données ;
* `NoteDao` définit les requêtes ;
* `NoteDatabase` représente la base Room ;
* `Note` représente la structure persistée ;
* `NoteAdapter` affiche les notes dans RecyclerView.

Cette organisation permet à chaque classe d’avoir une seule responsabilité principale.

## Flux détaillé d’une insertion

Le flux d’insertion complet est :

```
btnAdd clicked
    |
    v
MainActivity.saveNote()
    |
    v
new Note(title, description)
    |
    v
noteViewModel.insert(note)
    |
    v
repository.insert(note)
    |
    v
executorService.execute(...)
    |
    v
noteDao.insert(note)
    |
    v
Room
    |
    v
SQLite
    |
    v
LiveData<List<Note>> updated
    |
    v
Observer in MainActivity
    |
    v
adapter.setNotes(notes)
    |
    v
RecyclerView refreshed
```

## Flux détaillé d’une suppression

Le flux de suppression par clic long est :

```
Long click on note
    |
    v
NoteAdapter
    |
    v
OnItemLongClickListener
    |
    v
MainActivity
    |
    v
noteViewModel.delete(note)
    |
    v
repository.delete(note)
    |
    v
executorService.execute(...)
    |
    v
noteDao.delete(note)
    |
    v
Room / SQLite
    |
    v
LiveData emits new list
    |
    v
RecyclerView updates
```

## Bilan pédagogique

Ce laboratoire permet de maîtriser une architecture Android moderne et structurée.

Les notions principales abordées sont :

* Room
* SQLite
* Entity
* DAO
* RoomDatabase
* Repository
* ViewModel
* AndroidViewModel
* LiveData
* RecyclerView
* CardView
* Adapter
* ViewHolder
* ExecutorService
* MVVM
* persistance locale
* observation automatique

Le projet montre comment construire une application plus claire, plus fiable et plus maintenable qu’une application codée directement dans une Activity.

## Conclusion

Ce projet constitue une démonstration complète de l’utilisation de Room avec l’architecture MVVM dans une application Android Java.

L’application permet d’ajouter, afficher, supprimer et persister des notes localement. Grâce à Room, les données sont stockées dans SQLite de manière structurée. Grâce à LiveData, l’interface est automatiquement mise à jour à chaque changement. Grâce au ViewModel, l’écran reste stable lors des changements de configuration. Grâce au Repository, l’accès aux données reste propre et isolé.

Ce lab pose une base solide pour créer des applications Android plus avancées, notamment avec recherche, modification, synchronisation distante, pagination, injection de dépendances et architecture complète de production.



## Lab

LAB 19 : Room, MVVM, Repository, ViewModel, LiveData et RecyclerView
