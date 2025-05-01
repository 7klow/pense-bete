# 🚀 Pense-bête `Flutter & Dart` – Application Agricole
Un mémo rapide et clair pour développer avec Flutter/Dart 🌱
---

## 📋 Configuration du projet
| Commande | Description | Exemple |
|----------|-------------|---------|
| `flutter create` | 🆕 Créer un nouveau projet | `flutter create mon_app_agricole` |
| `flutter run` | ▶️ Exécuter l'application | `flutter run` ou `flutter run -d windows` |
| `flutter pub get` | 📦 Installer dépendances | `flutter pub get` |
| `flutter build` | 🔨 Compiler pour production | `flutter build apk` ou `flutter build windows` |
| `flutter doctor` | 🩺 Vérifier installation | `flutter doctor` |

---

## 💻 Bases Dart essentielles

### Variables et types
| Syntaxe | Description | Exemple |
|---------|-------------|---------|
| `var` | 🔄 Type inféré | `var temp = 25;` |
| `final` | 🔒 Variable non réassignable | `final date = DateTime.now();` |
| `const` | ⚙️ Constante de compilation | `const API_KEY = 'abc123';` |
| `String` | 📝 Texte | `String nom = 'Tomate';` |
| `int` | 🔢 Nombre entier | `int quantite = 50;` |
| `double` | 📊 Nombre décimal | `double prix = 2.99;` |
| `bool` | ✅ Booléen | `bool disponible = true;` |
| `List<T>` | 📋 Liste typée | `List<String> cultures = ['Tomate', 'Pomme'];` |
| `Map<K,V>` | 🗂️ Dictionnaire | `Map<String, double> prix = {'Tomate': 2.99};` |

```dart
// Exemple d'utilisation
final semis = {
  'nom': 'Tomate Roma',
  'quantite': 50,
  'datePlantation': DateTime.now(),
  'pret': false
};
```

### Fonctions et méthodes
| Syntaxe | Description | Exemple |
|---------|-------------|---------|
| `() {}` | 📋 Fonction standard | `void afficher() { print('Hello'); }` |
| `() => ` | 🏹 Fonction fléchée | `bool estMur(int j) => j >= 60;` |
| `{}` | 🏷️ Paramètres nommés | `void planter({required String nom})` |
| `async/await` | ⏳ Asynchrone | `Future<String> getData() async { await... }` |
| `try/catch` | 🛡️ Gestion erreurs | `try { ... } catch (e) { ... }` |

```dart
// Exemple de fonction avec paramètres nommés
void planterCulture({
  required String nom,
  int quantite = 1,
  String? emplacement,
}) {
  print('$quantite $nom planté(s) à $emplacement');
}

// Utilisation
planterCulture(nom: 'Tomate', quantite: 10, emplacement: 'Serre');
```

### Classes et objets
```dart
class Culture {
  // Propriétés
  final String nom;
  int quantite;
  double? prixParKg;
  
  // Constructeur
  Culture(this.nom, this.quantite, {this.prixParKg});
  
  // Méthode
  double valeurTotale() => quantite * (prixParKg ?? 0);
  
  // Getter
  bool get estValorisable => prixParKg != null && prixParKg! > 0;
}

// Utilisation
var tomates = Culture('Tomate', 50, prixParKg: 2.99);
print(tomates.valeurTotale()); // 149.5
```

---

## 🧩 Widgets Flutter principaux

### Structure d'application
| Widget | Description | Quand utiliser |
|--------|-------------|----------------|
| `MaterialApp` | 🏠 Racine d'application | Point d'entrée de l'app |
| `Scaffold` | 🏗️ Structure de page | Pour chaque écran |
| `AppBar` | 🔝 Barre supérieure | En-tête d'écran |
| `SafeArea` | 🛡️ Zone d'affichage sûre | Éviter les encoches |
| `Theme` | 🎨 Thème d'application | Styles globaux |

```dart
MaterialApp(
  title: 'AgriApp',
  theme: ThemeData(
    primarySwatch: Colors.green,
    brightness: Brightness.light,
  ),
  home: Scaffold(
    appBar: AppBar(title: Text('Tableau de bord')),
    body: Center(child: Text('AgriTech')),
  ),
);
```

### Mise en page
| Widget | Description | Exemple |
|--------|-------------|---------|
| `Container` | 📦 Boîte avec style | `Container(padding: EdgeInsets.all(16))` |
| `Row` | ↔️ Alignement horizontal | `Row(children: [Text('A'), Text('B')])` |
| `Column` | ↕️ Alignement vertical | `Column(children: [Text('1'), Text('2')])` |
| `Stack` | 📚 Superposition | `Stack(children: [Image(...), Text(...)])` |
| `Expanded` | 🔄 Espace flexible | `Expanded(child: Container(...))` |
| `Padding` | 📏 Marges intérieures | `Padding(padding: EdgeInsets.all(8))` |
| `SizedBox` | 📐 Espace fixe | `SizedBox(height: 16)` |
| `Card` | 🃏 Carte avec élévation | `Card(child: ListTile(...))` |
| `ListView` | 📜 Liste défilante | `ListView(children: [...])` |
| `GridView` | 📱 Grille | `GridView.count(crossAxisCount: 2)` |

```dart
// Exemple de mise en page
Container(
  padding: EdgeInsets.all(16),
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(8),
    boxShadow: [BoxShadow(blurRadius: 5, color: Colors.black12)],
  ),
  child: Column(
    children: [
      Text('Météo du jour', style: TextStyle(fontWeight: FontWeight.bold)),
      SizedBox(height: 8),
      Row(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: [
          Column(children: [Icon(Icons.wb_sunny), Text('22°C')]),
          Column(children: [Icon(Icons.water_drop), Text('65%')]),
        ],
      ),
    ],
  ),
)
```

### Widgets interactifs
| Widget | Description | Exemple |
|--------|-------------|---------|
| `ElevatedButton` | 🔘 Bouton en relief | `ElevatedButton(onPressed: () {}, child: Text('OK'))` |
| `TextButton` | 📝 Bouton texte | `TextButton(onPressed: () {}, child: Text('Plus'))` |
| `IconButton` | 🖼️ Bouton icône | `IconButton(onPressed: () {}, icon: Icon(Icons.add))` |
| `TextField` | ✏️ Champ de saisie | `TextField(onChanged: (v) => nom = v)` |
| `Checkbox` | ☑️ Case à cocher | `Checkbox(value: true, onChanged: (v) {})` |
| `Switch` | 🔄 Interrupteur | `Switch(value: active, onChanged: (v) {})` |
| `Slider` | 📊 Curseur | `Slider(value: 0.5, onChanged: (v) {})` |
| `DropdownButton` | 📋 Menu déroulant | `DropdownButton(items: [...], onChanged: (v) {})` |
| `GestureDetector` | 👆 Détecteur de gestes | `GestureDetector(onTap: () {}, child: Text('Tap'))` |

```dart
// Exemple de bouton avec style
ElevatedButton(
  style: ElevatedButton.styleFrom(
    backgroundColor: Colors.green,
    foregroundColor: Colors.white,
    padding: EdgeInsets.symmetric(horizontal: 20, vertical: 12),
    shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(8)),
  ),
  onPressed: () {
    print('Bouton pressé');
  },
  child: Text('Ajouter une culture'),
)
```

### Navigation
| Widget/Méthode | Description | Exemple |
|----------------|-------------|---------|
| `Navigator.push` | ➡️ Pousser page | `Navigator.push(context, MaterialPageRoute(...))` |
| `Navigator.pop` | ⬅️ Retour | `Navigator.pop(context)` |
| `TabBar` | 📑 Onglets | `TabBar(tabs: [Tab(text: 'Cultures'), ...])` |
| `BottomNavigationBar` | 🔽 Barre inférieure | `BottomNavigationBar(items: [BottomNavigationBarItem(...)])` |
| `Drawer` | 📲 Menu latéral | `Drawer(child: ListView(...))` |

```dart
// Navigation vers une autre page
ElevatedButton(
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => DetailCulture(culture: tomate)),
    );
  },
  child: Text('Voir détails'),
)
```

---

## 📱 Fonctionnalités pour app agricole

### 🌦️ API Météo (OpenWeatherMap)
```dart
// pubspec.yaml: http: ^0.13.4

import 'package:http/http.dart' as http;
import 'dart:convert';

Future<Map<String, dynamic>> obtenirMeteo(double lat, double lon) async {
  final apiKey = 'VOTRE_CLE_API';
  final url = 'https://api.openweathermap.org/data/2.5/weather?'
      'lat=$lat&lon=$lon&units=metric&appid=$apiKey';
      
  final response = await http.get(Uri.parse(url));
  
  if (response.statusCode == 200) {
    return jsonDecode(response.body);
  } else {
    throw Exception('Échec chargement météo: ${response.statusCode}');
  }
}

// Utilisation
try {
  final meteo = await obtenirMeteo(48.8566, 2.3522); // Paris
  print('Température: ${meteo['main']['temp']}°C');
  print('Conditions: ${meteo['weather'][0]['description']}');
} catch (e) {
  print('Erreur: $e');
}
```

### 🕸️ Web Scraping simple
```dart
// pubspec.yaml: html: ^0.15.0, http: ^0.13.4

import 'package:http/http.dart' as http;
import 'package:html/parser.dart' as parser;

Future<List<String>> extrairePrixAgricoles() async {
  final url = 'https://www.site-exemple.com/cours-agricoles';
  final resultats = <String>[];
  
  final response = await http.get(Uri.parse(url));
  
  if (response.statusCode == 200) {
    final document = parser.parse(response.body);
    final elements = document.querySelectorAll('table.prix tr');
    
    for (var element in elements) {
      final culture = element.querySelector('.nom')?.text.trim();
      final prix = element.querySelector('.prix')?.text.trim();
      
      if (culture != null && prix != null) {
        resultats.add('$culture: $prix');
      }
    }
  }
  
  return resultats;
}
```

### 💾 Stockage local
```dart
// pubspec.yaml: shared_preferences: ^2.0.13

import 'package:shared_preferences/shared_preferences.dart';

// Sauvegarder des données
Future<void> sauvegarderParametres(String nom, bool notifications) async {
  final prefs = await SharedPreferences.getInstance();
  
  await prefs.setString('nom_ferme', nom);
  await prefs.setBool('notifications', notifications);
}

// Charger des données
Future<Map<String, dynamic>> chargerParametres() async {
  final prefs = await SharedPreferences.getInstance();
  
  return {
    'nom_ferme': prefs.getString('nom_ferme') ?? 'Ma ferme',
    'notifications': prefs.getBool('notifications') ?? true,
  };
}
```

### 📅 Gestionnaire d'événements
```dart
// pubspec.yaml: table_calendar: ^3.0.6

import 'package:table_calendar/table_calendar.dart';

class Tache {
  final String titre;
  final String description;
  bool terminee;
  
  Tache(this.titre, this.description, {this.terminee = false});
}

// Dans un StatefulWidget
CalendarFormat _format = CalendarFormat.month;
DateTime _focusedDay = DateTime.now();
DateTime? _selectedDay;
Map<DateTime, List<Tache>> _taches = {};

// Widget calendrier
TableCalendar(
  firstDay: DateTime.utc(2021, 1, 1),
  lastDay: DateTime.utc(2030, 12, 31),
  focusedDay: _focusedDay,
  selectedDayPredicate: (day) => isSameDay(_selectedDay, day),
  onDaySelected: (selectedDay, focusedDay) {
    setState(() {
      _selectedDay = selectedDay;
      _focusedDay = focusedDay;
    });
  },
  calendarFormat: _format,
  onFormatChanged: (format) => setState(() => _format = format),
  eventLoader: (day) {
    return _taches[DateTime(day.year, day.month, day.day)] ?? [];
  },
)
```

### 📊 Visualisation de données
```dart
// pubspec.yaml: fl_chart: ^0.55.0

import 'package:fl_chart/fl_chart.dart';

// Graphique en ligne
LineChart(
  LineChartData(
    gridData: FlGridData(show: true),
    titlesData: FlTitlesData(show: true),
    borderData: FlBorderData(show: true),
    lineBarsData: [
      LineChartBarData(
        spots: [
          FlSpot(0, 18), // (jour, température)
          FlSpot(1, 22),
          FlSpot(2, 25),
          FlSpot(3, 23),
          FlSpot(4, 20),
        ],
        isCurved: true,
        color: Colors.blue,
        barWidth: 3,
        dotData: FlDotData(show: false),
      ),
    ],
  ),
)

// Graphique en barres
BarChart(
  BarChartData(
    alignment: BarChartAlignment.center,
    maxY: 100,
    titlesData: FlTitlesData(
      leftTitles: SideTitles(showTitles: true),
      bottomTitles: SideTitles(
        showTitles: true,
        getTitles: (value) {
          switch (value.toInt()) {
            case 0: return 'Blé';
            case 1: return 'Maïs';
            default: return '';
          }
        },
      ),
    ),
    barGroups: [
      BarChartGroupData(
        x: 0,
        barRods: [BarChartRodData(y: 75, colors: [Colors.amber])],
      ),
      BarChartGroupData(
        x: 1,
        barRods: [BarChartRodData(y: 90, colors: [Colors.green])],
      ),
    ],
  ),
)
```

### 🔔 Notifications locales
```dart
// pubspec.yaml: flutter_local_notifications: ^9.5.3+1

import 'package:flutter_local_notifications/flutter_local_notifications.dart';

final notifications = FlutterLocalNotificationsPlugin();

// Initialisation
Future<void> initNotifications() async {
  const androidSettings = AndroidInitializationSettings('@mipmap/ic_launcher');
  const iosSettings = IOSInitializationSettings();
  
  await notifications.initialize(
    InitializationSettings(android: androidSettings, iOS: iosSettings),
    onSelectNotification: (payload) async {
      // Action quand notification cliquée
      print('Notification: $payload');
    },
  );
}

// Afficher notification
Future<void> afficherNotification({
  required String titre,
  required String corps,
  String? payload,
}) async {
  const androidDetails = AndroidNotificationDetails(
    'channel_id',
    'Canal principal',
    importance: Importance.max,
    priority: Priority.high,
  );
  
  const iosDetails = IOSNotificationDetails();
  const details = NotificationDetails(android: androidDetails, iOS: iosDetails);
  
  await notifications.show(0, titre, corps, details, payload: payload);
}

// Programmer notification
Future<void> programmerNotification({
  required String titre,
  required String corps,
  required DateTime quand,
}) async {
  const androidDetails = AndroidNotificationDetails(
    'channel_id',
    'Canal principal',
    importance: Importance.max,
  );
  
  const iosDetails = IOSNotificationDetails();
  const details = NotificationDetails(android: androidDetails, iOS: iosDetails);
  
  await notifications.zonedSchedule(
    1,
    titre,
    corps,
    TZDateTime.from(quand, local),
    details,
    androidAllowWhileIdle: true,
    uiLocalNotificationDateInterpretation:
        UILocalNotificationDateInterpretation.absoluteTime,
  );
}
```

---

## 🔧 Outils et APIs agricoles utiles

### 📡 APIs recommandées
| Nom | Description | URL |
|-----|-------------|-----|
| OpenWeatherMap | 🌦️ Météo détaillée | https://openweathermap.org/api |
| aWhere | 🌱 Données agronomiques | https://developer.awhere.com/ |
| Agrimetrics | 📊 Données agricoles UK | https://app.agrimetrics.co.uk/ |
| FarmOS | 🚜 Gestion ferme API | https://farmos.org/ |
| OpenFarm | 🌿 Données culturales | https://openfarm.cc/api |

### 🛠️ Packages Flutter utiles
| Package | Description | Installation |
|---------|-------------|-------------|
| `http` | 🌐 Requêtes HTTP | `flutter pub add http` |
| `sqflite` | 💾 Base SQL locale | `flutter pub add sqflite` |
| `shared_preferences` | 🔧 Stockage simple | `flutter pub add shared_preferences` |
| `provider` | 🧩 Gestion d'état | `flutter pub add provider` |
| `fl_chart` | 📊 Graphiques | `flutter pub add fl_chart` |
| `table_calendar` | 📅 Calendrier | `flutter pub add table_calendar` |
| `image_picker` | 📷 Photos | `flutter pub add image_picker` |
| `geolocator` | 📍 Géolocalisation | `flutter pub add geolocator` |
| `flutter_map` | 🗺️ Cartes | `flutter pub add flutter_map` |
| `permission_handler` | 🔓 Permissions | `flutter pub add permission_handler` |

---

## 💡 Exemples de code pour app agricole

### 📱 Structure de projet recommandée
```
mon_app_agricole/
├── lib/
│   ├── main.dart              # Point d'entrée
│   ├── models/                # Classes de données
│   │   ├── culture.dart
│   │   └── meteo.dart
│   ├── screens/               # Écrans
│   │   ├── dashboard.dart
│   │   ├── meteo_screen.dart
│   │   └── cultures_screen.dart
│   ├── services/              # Services (API, DB)
│   │   ├── meteo_service.dart
│   │   └── storage_service.dart
│   ├── utils/                 # Utilitaires
│   │   └── constants.dart
│   └── widgets/               # Widgets réutilisables
│       ├── meteo_card.dart
│       └── culture_item.dart
└── pubspec.yaml               # Dépendances
```

### 🎨 Thème personnalisé
```dart
ThemeData(
  primarySwatch: Colors.green,
  brightness: Brightness.light,
  appBarTheme: AppBarTheme(
    backgroundColor: Colors.green,
    foregroundColor: Colors.white,
  ),
  elevatedButtonTheme: ElevatedButtonThemeData(
    style: ElevatedButton.styleFrom(
      backgroundColor: Colors.green,
      foregroundColor: Colors.white,
      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(8)),
    ),
  ),
  cardTheme: CardTheme(
    elevation: 3,
    shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
    clipBehavior: Clip.antiAlias,
  ),
)
```

### 🌡️ Widget météo simple
```dart
class MeteoWidget extends StatelessWidget {
  final String condition;
  final double temperature;
  final double humidite;
  
  const MeteoWidget({
    required this.condition,
    required this.temperature,
    required this.humidite,
  });
  
  IconData _getWeatherIcon() {
    switch (condition.toLowerCase()) {
      case 'clear': return Icons.wb_sunny;
      case 'clouds': return Icons.cloud;
      case 'rain': return Icons.grain;
      default: return Icons.wb_cloudy;
    }
  }
  
  @override
  Widget build(BuildContext context) {
    return Card(
      child: Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.center,
          children: [
            Text('Météo actuelle',
              style: TextStyle(fontWeight: FontWeight.bold, fontSize: 18),
            ),
            SizedBox(height: 8),
            Icon(_getWeatherIcon(), size: 48, color: Colors.amber),
            Text('$temperature°C',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            SizedBox(height: 8),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                Icon(Icons.water_drop, color: Colors.blue),
                Text('$humidite%'),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
```

### 🚜 Modèle culture
```dart
enum StatutCulture { semis, croissance, recolte, termine }

class Culture {
  final String id;
  final String nom;
  final String variete;
  DateTime datePlantation;
  int dureeJusquaRecolte;
  StatutCulture statut;
  String? notes;
  
  Culture({
    required this.id,
    required this.nom,
    required this.variete,
    required this.datePlantation,
    required this.dureeJusquaRecolte,
    this.statut = StatutCulture.semis,
    this.notes,
  });
  
  // Pourcentage de progression vers la récolte
  double get progression {
    final joursEcoules = DateTime.now().difference(datePlantation).inDays;
    return (joursEcoules / dureeJusquaRecolte).clamp(0.0, 1.0);
  }
  
  // Date estimée de récolte
  DateTime get dateRecolteEstimee {
    return datePlantation.add(Duration(days: dureeJusquaRecolte));
  }
  
  // Jours restants avant récolte
  int get joursAvantRecolte {
    final difference = dateRecolteEstimee.difference(DateTime.now()).inDays;
    return difference < 0 ? 0 : difference;
  }
  
  // Conversion Map pour stockage
  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'nom': nom,
      'variete': variete,
      'datePlantation': datePlantation.millisecondsSinceEpoch,
      'dureeJusquaRecolte': dureeJusquaRecolte,
      'statut': statut.index,
      'notes': notes,
    };
  }
  
  // Création depuis Map
  factory Culture.fromMap(Map<String, dynamic> map) {
    return Culture(
      id: map['id'],
      nom: map['nom'],
      variete: map['variete'],
      datePlantation: DateTime.fromMillisecondsSinceEpoch(map['datePlantation']),
      dureeJusquaRecolte: map['dureeJusquaRecolte'],
      statut: StatutCulture.values[map['statut']],
      notes: map['notes'],
    );
  }
}
```

### 📱 Écran liste de cultures
```dart
class ListeCulturesScreen extends StatelessWidget {
  final List<Culture> cultures;
  
  const ListeCulturesScreen({required this.cultures});
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Mes cultures')),
      body: cultures.isEmpty
          ? Center(child: Text('Aucune culture enregistrée'))
          : ListView.builder(
              itemCount: cultures.length,
              itemBuilder: (context, index) {
                final culture = cultures[index];
                return Card(
                  margin: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
                  child: ListTile(
                    leading: CircleAvatar(
                      backgroundColor: _getStatusColor(culture.statut),
                      child: Icon(Icons.spa, color: Colors.white),
                    ),
                    title: Text(
                      '${culture.nom} (${culture.variete})',
                      style: TextStyle(fontWeight: FontWeight.bold),
                    ),
                    subtitle: Column(
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        Text('Planté le ${_formatDate(culture.datePlantation)}'),
                        SizedBox(height: 4),
                        LinearProgressIndicator(value: culture.progression),
                        SizedBox(height: 4),
                        Text(
                          culture.joursAvantRecolte > 0
                              ? 'Récolte dans ${culture.joursAvantRecolte} jours'
                              : 'Prêt à récolter !',
                        ),
                      ],
                    ),
                    trailing: Icon(Icons.chevron_right),
                    onTap: () {
                      // Navigation vers détails
                    },
                  ),
                );
              },
            ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () {
          // Ajouter une culture
        },
      ),
    );
  }
  
  Color _getStatusColor(StatutCulture statut) {
    switch (statut) {
      case StatutCulture.semis: return Colors.amber;
      case StatutCulture.croissance: return Colors.green;
      case StatutCulture.recolte: return Colors.red;
      case StatutCulture.termine: return Colors.grey;
    }
  }
  
  String _formatDate(DateTime date) {
    return '${date.day}/${date.month}/${date.year}';
  }
}
```