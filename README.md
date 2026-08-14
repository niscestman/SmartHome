# Smart Home – kotlin_App

## 📋 Description

Le programme simule un système domotique (*smart home*) composé de deux appareils connectés :
- une **Smart TV** (volume, changement de chaîne)
- une **Smart Light** (luminosité)

Chaque appareil hérite d'une classe de base commune `SmartDevice`, et l'ensemble est piloté par une classe `SmartHome` qui centralise les actions et garde le compte des appareils allumés.

## 🏗️ Architecture

### `SmartDevice` (classe ouverte)
Classe parente définissant le comportement commun à tous les appareils connectés :
- `name`, `category` : propriétés immuables
- `deviceStatus` : statut de l'appareil (`online`, `offline`, `on`, `off`), modifiable uniquement en interne (`protected set`)
- `deviceType` : propriété ouverte, redéfinie par chaque sous-classe
- Un **constructeur secondaire** permet d'initialiser le statut à partir d'un code numérique (0 = offline, 1 = online)
- Méthodes ouvertes `turnOn()` / `turnOff()` et méthode utilitaire `printDeviceInfo()`

### `SmartTvDevice` et `SmartLightDevice`
Sous-classes de `SmartDevice`, chacune :
- redéfinit `deviceType`
- redéfinit `turnOn()` / `turnOff()` en appelant `super` puis en ajoutant son propre comportement
- possède ses propres propriétés bornées (volume/chaîne pour la TV, luminosité pour la lampe) grâce à `RangeRegulator`

### `RangeRegulator` (propriété déléguée)
Implémente `ReadWriteProperty<Any?, Int>` pour garantir qu'une valeur reste toujours comprise entre un `minValue` et un `maxValue` (via `coerceIn`). Utilisée en délégation avec le mot-clé `by` :
```kotlin
private var speakerVolume by RangeRegulator(initialValue = 2, minValue = 0, maxValue = 100)
```

### `SmartHome`
Classe orchestratrice qui expose des actions de haut niveau (`turnOnTv`, `turnOffLight`, `increaseTvVolume`, etc.) et maintient un compteur `deviceTurnOnCount` du nombre d'appareils actuellement allumés.

## ▶️ Exécution

Le moyen le plus simple d'exécuter ce projet est d'utiliser le **Kotlin Playground**, sans rien installer :

1. Rendez-vous sur [https://play.kotlinlang.org/](https://play.kotlinlang.org/)
2. Copiez-collez l'intégralité du contenu du fichier `SmartHome.kt`
3. Cliquez sur **Run** ▶️ pour voir le résultat s'afficher directement dans le navigateur

Vous pouvez aussi l'exécuter localement depuis IntelliJ IDEA / Android Studio en lançant la fonction `main()`.

## 🖥️ Exemple de sortie

```
Speaker volume increased to 3.
Device name: Android TV, category: Entertainment, type: Smart TV
Android TV is turned on. Speaker volume is set to 2 and channel number is set to 1.
Speaker volume increased to 3.
Speaker volume decreased to 2.
Channel number increased to 2.
Channel number decreased to 1.
Device name: Android TV, category: Entertainment, type: Smart TV
Google Light turned on. The brightness level is 2.
Device name: Google Light, category: Utility, type: Smart Light
Brightness decreased to 1.
deviceTurnOnCount = 2
Android TV turned off
Smart Light turned off
deviceTurnOnCount = 0
```

## 🧠 Concepts Kotlin illustrés

| Concept | Où le trouver |
|---|---|
| Classes ouvertes / héritage | `open class SmartDevice`, `: SmartDevice(...)` |
| Constructeur secondaire | `constructor(name, category, statusCode)` |
| Visibilité `protected set` | `deviceStatus` |
| Redéfinition (`override`) | `deviceType`, `turnOn()`, `turnOff()` |
| Appel au parent (`super`) | `SmartTvDevice.turnOn()` |
| Propriétés déléguées | `RangeRegulator` + mot-clé `by` |
| Interface `ReadWriteProperty` | `class RangeRegulator` |
| Encapsulation | Compteur privé `deviceTurnOnCount` avec `private set` |

## 📁 Structure du dépôt

```
.
├── SmartHome.kt
└── README.md
```

## 👤 Auteur

Projet réalisé dans le cadre d'un apprentissage de Kotlin (POO avancée / Jetpack).
